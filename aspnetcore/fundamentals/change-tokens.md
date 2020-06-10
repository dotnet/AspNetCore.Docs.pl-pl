---
title: Wykrywanie zmian przy użyciu tokenów zmiany w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak śledzić zmiany przy użyciu tokenów zmian.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: f581e26b0d11923d9cf099700c4338c99e68bb59
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106614"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="03bda-103">Wykrywanie zmian przy użyciu tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03bda-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03bda-104">*Tokenem zmiany* jest ogólnym blokiem konstrukcyjnym, który służy do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="03bda-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="03bda-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="03bda-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="03bda-106">IChangeToken, interfejs</span><span class="sxs-lookup"><span data-stu-id="03bda-106">IChangeToken interface</span></span>

<span data-ttu-id="03bda-107"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="03bda-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="03bda-108">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="03bda-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="03bda-109">Pakiet NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03bda-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="03bda-110">`IChangeToken`ma dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="03bda-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="03bda-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskaż, czy token aktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="03bda-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="03bda-112">Jeśli `ActiveChangedCallbacks` jest ustawiona na `false` , wywołanie zwrotne nigdy nie zostanie wywołane, a aplikacja musi sondować `HasChanged` pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="03bda-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="03bda-113">Istnieje również możliwość, że token nigdy nie zostanie anulowany, jeśli nie wystąpią żadne zmiany lub zostanie usunięty lub wyłączony odbiornik zmian.</span><span class="sxs-lookup"><span data-stu-id="03bda-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="03bda-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>odbiera wartość wskazującą, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="03bda-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="03bda-115">`IChangeToken`Interfejs zawiera metodę [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , która rejestruje wywołanie zwrotne, które jest wywoływane, gdy token został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="03bda-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="03bda-116">`HasChanged`musi być ustawiona przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="03bda-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="03bda-117">Klasa ChangeToken</span><span class="sxs-lookup"><span data-stu-id="03bda-117">ChangeToken class</span></span>

<span data-ttu-id="03bda-118"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną służącą do propagowania powiadomień, w których wystąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="03bda-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="03bda-119">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="03bda-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="03bda-120">Pakiet NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03bda-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="03bda-121">Metoda [ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywoływanie przy każdym zmianie tokenu:</span><span class="sxs-lookup"><span data-stu-id="03bda-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="03bda-122">`Func<IChangeToken>`tworzy token.</span><span class="sxs-lookup"><span data-stu-id="03bda-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="03bda-123">`Action`jest wywoływana, gdy zostanie zmieniony token.</span><span class="sxs-lookup"><span data-stu-id="03bda-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="03bda-124">Przeciążenie [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest przesyłany do odbiorcy tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="03bda-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="03bda-125">`OnChange`Zwraca wartość <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="03bda-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="03bda-126">Wywołanie <xref:System.IDisposable.Dispose*> uniemożliwia wysłuchanie tokenu w celu wprowadzenia dalszych zmian i zwolnienia zasobów tokenu.</span><span class="sxs-lookup"><span data-stu-id="03bda-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="03bda-127">Przykładowe zastosowania tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03bda-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="03bda-128">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian obiektów:</span><span class="sxs-lookup"><span data-stu-id="03bda-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="03bda-129">W przypadku monitorowania zmian w plikach <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> Metoda tworzy `IChangeToken` dla określonych plików lub folderów, które mają być monitorowane.</span><span class="sxs-lookup"><span data-stu-id="03bda-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="03bda-130">`IChangeToken`można dodać tokeny do wpisów pamięci podręcznej, aby wyzwolić wykluczenia z pamięci podręcznej w przypadku zmiany.</span><span class="sxs-lookup"><span data-stu-id="03bda-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="03bda-131">W przypadku `TOptions` zmian Domyślna <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma Przeciążenie, które akceptuje co najmniej jedno <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="03bda-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="03bda-132">Każde wystąpienie zwraca wartość, `IChangeToken` Aby zarejestrować zmiany zwrotne powiadomień o zmianach opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="03bda-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="03bda-133">Monitorowanie zmian konfiguracji</span><span class="sxs-lookup"><span data-stu-id="03bda-133">Monitor for configuration changes</span></span>

<span data-ttu-id="03bda-134">Domyślnie szablony ASP.NET Core korzystają z [plików konfiguracji JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Development. JSON*i *appSettings. Production. JSON*) w celu załadowania ustawień konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="03bda-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="03bda-135">Te pliki są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="03bda-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="03bda-136">`reloadOnChange`wskazuje, czy należy ponownie załadować konfigurację w przypadku zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="03bda-137">To ustawienie jest dostępne w <xref:Microsoft.Extensions.Hosting.Host> metodzie wygodnej <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="03bda-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="03bda-138">Konfiguracja oparta na plikach jest reprezentowana przez <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="03bda-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="03bda-139">`FileConfigurationSource`program używa <xref:Microsoft.Extensions.FileProviders.IFileProvider> do monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="03bda-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="03bda-140">Domyślnie `IFileMonitor` jest on dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , który służy <xref:System.IO.FileSystemWatcher> do monitorowania zmian w pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="03bda-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="03bda-141">Przykładowa aplikacja przedstawia dwie implementacje monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="03bda-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="03bda-142">Jeśli którykolwiek z plików *AppSettings* zostanie zmieniony, obydwie implementacje monitorowania plików wykonują kod niestandardowy, &mdash; a Przykładowa aplikacja zapisuje komunikat w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="03bda-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="03bda-143">Plik konfiguracji `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenów dla jednej zmiany pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="03bda-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="03bda-144">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz w przypadku wyzwolenia wielu wywołań zwrotnych tokenów, implementacja próbki sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="03bda-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="03bda-145">W przykładzie zastosowano mieszanie plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="03bda-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="03bda-146">Ponowna próba jest zaimplementowana z wycofywaniem wykładniczym.</span><span class="sxs-lookup"><span data-stu-id="03bda-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="03bda-147">Ponowienie próby jest możliwe, ponieważ może wystąpić zablokowanie pliku, który tymczasowo uniemożliwia Obliczanie nowego skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="03bda-148">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="03bda-149">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="03bda-149">Simple startup change token</span></span>

<span data-ttu-id="03bda-150">Zarejestruj `Action` wywołanie zwrotne odbiorcy tokenu dla powiadomień o zmianach w tokenie Załaduj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="03bda-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="03bda-151">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="03bda-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="03bda-152">`config.GetReloadToken()`udostępnia token.</span><span class="sxs-lookup"><span data-stu-id="03bda-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="03bda-153">Wywołanie zwrotne to `InvokeChanged` Metoda:</span><span class="sxs-lookup"><span data-stu-id="03bda-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="03bda-154">`state`Wywołanie zwrotne jest używane do przekazywania w `IWebHostEnvironment` , co jest przydatne do określenia poprawnego pliku konfiguracyjnego *AppSettings* do monitorowania (na przykład *appSettings. Plik Development. JSON* w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="03bda-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="03bda-155">Skróty plików są używane, aby zapobiec `WriteConsole` uruchamianiu instrukcji wiele razy z powodu wywołania zwrotnego wielu tokenów, gdy plik konfiguracyjny został zmieniony tylko raz.</span><span class="sxs-lookup"><span data-stu-id="03bda-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="03bda-156">Ten system działa tak długo, jak działa aplikacja i nie może zostać wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03bda-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="03bda-157">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="03bda-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="03bda-158">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="03bda-158">The sample implements:</span></span>

* <span data-ttu-id="03bda-159">Podstawowe monitorowanie tokenów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="03bda-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="03bda-160">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="03bda-160">Monitoring as a service.</span></span>
* <span data-ttu-id="03bda-161">Mechanizm do włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="03bda-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="03bda-162">Przykład ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="03bda-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="03bda-163">*Rozszerzenia/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="03bda-164">Konstruktor zaimplementowanej klasy, `ConfigurationMonitor` rejestruje wywołanie zwrotne dla powiadomień o zmianach:</span><span class="sxs-lookup"><span data-stu-id="03bda-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="03bda-165">`config.GetReloadToken()`dostarcza token.</span><span class="sxs-lookup"><span data-stu-id="03bda-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="03bda-166">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="03bda-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="03bda-167">`state`W tym wystąpieniu jest odwołanie do `IConfigurationMonitor` wystąpienia, które jest używane w celu uzyskania dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="03bda-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="03bda-168">Są używane dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="03bda-168">Two properties are used:</span></span>

* <span data-ttu-id="03bda-169">`MonitoringEnabled`: Wskazuje, czy wywołanie zwrotne powinno uruchamiać swój kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="03bda-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="03bda-170">`CurrentState`: Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03bda-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="03bda-171">`InvokeChanged`Metoda jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="03bda-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="03bda-172">Kod nie jest uruchamiany, chyba że `MonitoringEnabled` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="03bda-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="03bda-173">Wyprowadza bieżącą wartość `state` w jej `WriteConsole` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="03bda-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="03bda-174">Wystąpienie `ConfigurationMonitor` jest zarejestrowane jako usługa w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="03bda-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="03bda-175">Strona indeks zapewnia kontrolę nad konfiguracją przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03bda-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="03bda-176">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="03bda-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="03bda-177">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="03bda-178">Monitor konfiguracji ( `_monitor` ) służy do włączania lub wyłączania monitorowania oraz ustawiania bieżącego stanu dla opinii o interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="03bda-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="03bda-179">Gdy `OnPostStartMonitoring` jest wyzwalane, monitorowanie jest włączone, a bieżący stan jest wyczyszczony.</span><span class="sxs-lookup"><span data-stu-id="03bda-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="03bda-180">Gdy `OnPostStopMonitoring` jest wyzwalane, monitorowanie jest wyłączone, a stan jest ustawiony na odzwierciedlenie tego, że monitorowanie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="03bda-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="03bda-181">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="03bda-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="03bda-182">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="03bda-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="03bda-183">Monitoruj zmiany plików w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="03bda-183">Monitor cached file changes</span></span>

<span data-ttu-id="03bda-184">Zawartość pliku może być buforowana w pamięci za pomocą polecenia <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="03bda-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="03bda-185">Buforowanie w pamięci jest opisane w temacie [pamięć podręczna w pamięci podręcznej](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="03bda-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="03bda-186">Bez podejmowania dodatkowych czynności, takich jak implementacja opisana poniżej, *nieodświeżone (przestarzałe* ) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe zostaną zmienione.</span><span class="sxs-lookup"><span data-stu-id="03bda-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="03bda-187">Na przykład nie uwzględnia stanu pliku źródłowego w pamięci podręcznej podczas odnawiania przedziału czasu [wygaśnięcia](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="03bda-188">Każde żądanie dotyczące danych odnawia przedział czasu wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="03bda-189">Wszystkie funkcje aplikacji korzystające z zawartości w pamięci podręcznej są uzależnione od potencjalnie otrzymywania nieodświeżonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="03bda-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="03bda-190">Użycie tokenów zmiany w scenariuszu buforowania plików uniemożliwia obecność przestarzałych zawartości plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="03bda-191">Przykładowa aplikacja prezentuje implementację metody.</span><span class="sxs-lookup"><span data-stu-id="03bda-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="03bda-192">Przykład używa `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="03bda-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="03bda-193">Zwróć zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-193">Return file content.</span></span>
* <span data-ttu-id="03bda-194">Zaimplementuj algorytm ponawiania prób z wycofywaniem z powrotem, aby uwzględnić przypadki, w których blokada pliku tymczasowo uniemożliwia odczytywanie pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="03bda-195">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="03bda-196">`FileService`Jest tworzony w celu obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="03bda-197">`GetFileContent`Wywołanie metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci i zwrócić ją do obiektu wywołującego (*Services/FileService. cs*).</span><span class="sxs-lookup"><span data-stu-id="03bda-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="03bda-198">Jeśli buforowana zawartość nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną wykonane następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="03bda-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="03bda-199">Zawartość pliku jest uzyskiwana przy użyciu `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="03bda-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="03bda-200">Token zmiany jest uzyskiwany od dostawcy plików z [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="03bda-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="03bda-201">Wywołanie zwrotne tokenu jest wyzwalane, gdy plik zostanie zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="03bda-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="03bda-202">Zawartość pliku jest buforowana z [ruchomym](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) okresem ważności.</span><span class="sxs-lookup"><span data-stu-id="03bda-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="03bda-203">Token zmiany jest dołączony do [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) w celu wykluczenia wpisu pamięci podręcznej, jeśli plik zostanie zmieniony w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="03bda-204">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="03bda-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="03bda-205">`IWebHostEnvironment.ContentRootFileProvider`służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> wskazujący na aplikację `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="03bda-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="03bda-206">`filePath`Jest on uzyskiwany za pomocą [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="03bda-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="03bda-207">`FileService`Program jest zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="03bda-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="03bda-208">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="03bda-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="03bda-209">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="03bda-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="03bda-210">Na stronie indeksu `OnGet` (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="03bda-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="03bda-211">Klasa CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="03bda-211">CompositeChangeToken class</span></span>

<span data-ttu-id="03bda-212">Dla reprezentowania jednego lub większej liczby `IChangeToken` wystąpień w pojedynczym obiekcie, użyj <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> klasy.</span><span class="sxs-lookup"><span data-stu-id="03bda-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="03bda-213">`HasChanged`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `HasChanged` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="03bda-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="03bda-214">`ActiveChangeCallbacks`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `ActiveChangeCallbacks` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="03bda-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="03bda-215">W przypadku wystąpienia wielu współbieżnych zdarzeń zmiany wywołanie zwrotne zmiany złożonego jest wywoływana jednokrotnie.</span><span class="sxs-lookup"><span data-stu-id="03bda-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03bda-216">*Tokenem zmiany* jest ogólnym blokiem konstrukcyjnym, który służy do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="03bda-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="03bda-217">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="03bda-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="03bda-218">IChangeToken, interfejs</span><span class="sxs-lookup"><span data-stu-id="03bda-218">IChangeToken interface</span></span>

<span data-ttu-id="03bda-219"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="03bda-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="03bda-220">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="03bda-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="03bda-221">W przypadku aplikacji, które nie korzystają z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu dla pakietu NuGet [Microsoft. Extensions. pierwotne](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="03bda-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="03bda-222">`IChangeToken`ma dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="03bda-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="03bda-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskaż, czy token aktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="03bda-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="03bda-224">Jeśli `ActiveChangedCallbacks` jest ustawiona na `false` , wywołanie zwrotne nigdy nie zostanie wywołane, a aplikacja musi sondować `HasChanged` pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="03bda-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="03bda-225">Istnieje również możliwość, że token nigdy nie zostanie anulowany, jeśli nie wystąpią żadne zmiany lub zostanie usunięty lub wyłączony odbiornik zmian.</span><span class="sxs-lookup"><span data-stu-id="03bda-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="03bda-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>odbiera wartość wskazującą, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="03bda-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="03bda-227">`IChangeToken`Interfejs zawiera metodę [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , która rejestruje wywołanie zwrotne, które jest wywoływane, gdy token został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="03bda-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="03bda-228">`HasChanged`musi być ustawiona przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="03bda-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="03bda-229">Klasa ChangeToken</span><span class="sxs-lookup"><span data-stu-id="03bda-229">ChangeToken class</span></span>

<span data-ttu-id="03bda-230"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną służącą do propagowania powiadomień, w których wystąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="03bda-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="03bda-231">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="03bda-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="03bda-232">W przypadku aplikacji, które nie korzystają z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu dla pakietu NuGet [Microsoft. Extensions. pierwotne](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="03bda-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="03bda-233">Metoda [ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywoływanie przy każdym zmianie tokenu:</span><span class="sxs-lookup"><span data-stu-id="03bda-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="03bda-234">`Func<IChangeToken>`tworzy token.</span><span class="sxs-lookup"><span data-stu-id="03bda-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="03bda-235">`Action`jest wywoływana, gdy zostanie zmieniony token.</span><span class="sxs-lookup"><span data-stu-id="03bda-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="03bda-236">Przeciążenie [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest przesyłany do odbiorcy tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="03bda-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="03bda-237">`OnChange`Zwraca wartość <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="03bda-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="03bda-238">Wywołanie <xref:System.IDisposable.Dispose*> uniemożliwia wysłuchanie tokenu w celu wprowadzenia dalszych zmian i zwolnienia zasobów tokenu.</span><span class="sxs-lookup"><span data-stu-id="03bda-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="03bda-239">Przykładowe zastosowania tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03bda-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="03bda-240">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian obiektów:</span><span class="sxs-lookup"><span data-stu-id="03bda-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="03bda-241">W przypadku monitorowania zmian w plikach <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> Metoda tworzy `IChangeToken` dla określonych plików lub folderów, które mają być monitorowane.</span><span class="sxs-lookup"><span data-stu-id="03bda-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="03bda-242">`IChangeToken`można dodać tokeny do wpisów pamięci podręcznej, aby wyzwolić wykluczenia z pamięci podręcznej w przypadku zmiany.</span><span class="sxs-lookup"><span data-stu-id="03bda-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="03bda-243">W przypadku `TOptions` zmian Domyślna <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma Przeciążenie, które akceptuje co najmniej jedno <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="03bda-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="03bda-244">Każde wystąpienie zwraca wartość, `IChangeToken` Aby zarejestrować zmiany zwrotne powiadomień o zmianach opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="03bda-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="03bda-245">Monitorowanie zmian konfiguracji</span><span class="sxs-lookup"><span data-stu-id="03bda-245">Monitor for configuration changes</span></span>

<span data-ttu-id="03bda-246">Domyślnie szablony ASP.NET Core korzystają z [plików konfiguracji JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Development. JSON*i *appSettings. Production. JSON*) w celu załadowania ustawień konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="03bda-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="03bda-247">Te pliki są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="03bda-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="03bda-248">`reloadOnChange`wskazuje, czy należy ponownie załadować konfigurację w przypadku zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="03bda-249">To ustawienie jest dostępne w <xref:Microsoft.AspNetCore.WebHost> metodzie wygodnej <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="03bda-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="03bda-250">Konfiguracja oparta na plikach jest reprezentowana przez <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="03bda-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="03bda-251">`FileConfigurationSource`program używa <xref:Microsoft.Extensions.FileProviders.IFileProvider> do monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="03bda-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="03bda-252">Domyślnie `IFileMonitor` jest on dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , który służy <xref:System.IO.FileSystemWatcher> do monitorowania zmian w pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="03bda-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="03bda-253">Przykładowa aplikacja przedstawia dwie implementacje monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="03bda-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="03bda-254">Jeśli którykolwiek z plików *AppSettings* zostanie zmieniony, obydwie implementacje monitorowania plików wykonują kod niestandardowy, &mdash; a Przykładowa aplikacja zapisuje komunikat w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="03bda-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="03bda-255">Plik konfiguracji `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenów dla jednej zmiany pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="03bda-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="03bda-256">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz w przypadku wyzwolenia wielu wywołań zwrotnych tokenów, implementacja próbki sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="03bda-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="03bda-257">W przykładzie zastosowano mieszanie plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="03bda-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="03bda-258">Ponowna próba jest zaimplementowana z wycofywaniem wykładniczym.</span><span class="sxs-lookup"><span data-stu-id="03bda-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="03bda-259">Ponowienie próby jest możliwe, ponieważ może wystąpić zablokowanie pliku, który tymczasowo uniemożliwia Obliczanie nowego skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="03bda-260">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="03bda-261">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="03bda-261">Simple startup change token</span></span>

<span data-ttu-id="03bda-262">Zarejestruj `Action` wywołanie zwrotne odbiorcy tokenu dla powiadomień o zmianach w tokenie Załaduj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="03bda-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="03bda-263">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="03bda-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="03bda-264">`config.GetReloadToken()`udostępnia token.</span><span class="sxs-lookup"><span data-stu-id="03bda-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="03bda-265">Wywołanie zwrotne to `InvokeChanged` Metoda:</span><span class="sxs-lookup"><span data-stu-id="03bda-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="03bda-266">`state`Wywołanie zwrotne jest używane do przekazywania w `IHostingEnvironment` , co jest przydatne do określenia poprawnego pliku konfiguracyjnego *AppSettings* do monitorowania (na przykład *appSettings. Plik Development. JSON* w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="03bda-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="03bda-267">Skróty plików są używane, aby zapobiec `WriteConsole` uruchamianiu instrukcji wiele razy z powodu wywołania zwrotnego wielu tokenów, gdy plik konfiguracyjny został zmieniony tylko raz.</span><span class="sxs-lookup"><span data-stu-id="03bda-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="03bda-268">Ten system działa tak długo, jak działa aplikacja i nie może zostać wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03bda-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="03bda-269">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="03bda-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="03bda-270">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="03bda-270">The sample implements:</span></span>

* <span data-ttu-id="03bda-271">Podstawowe monitorowanie tokenów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="03bda-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="03bda-272">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="03bda-272">Monitoring as a service.</span></span>
* <span data-ttu-id="03bda-273">Mechanizm do włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="03bda-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="03bda-274">Przykład ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="03bda-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="03bda-275">*Rozszerzenia/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="03bda-276">Konstruktor zaimplementowanej klasy, `ConfigurationMonitor` rejestruje wywołanie zwrotne dla powiadomień o zmianach:</span><span class="sxs-lookup"><span data-stu-id="03bda-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="03bda-277">`config.GetReloadToken()`dostarcza token.</span><span class="sxs-lookup"><span data-stu-id="03bda-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="03bda-278">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="03bda-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="03bda-279">`state`W tym wystąpieniu jest odwołanie do `IConfigurationMonitor` wystąpienia, które jest używane w celu uzyskania dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="03bda-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="03bda-280">Są używane dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="03bda-280">Two properties are used:</span></span>

* <span data-ttu-id="03bda-281">`MonitoringEnabled`: Wskazuje, czy wywołanie zwrotne powinno uruchamiać swój kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="03bda-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="03bda-282">`CurrentState`: Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03bda-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="03bda-283">`InvokeChanged`Metoda jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="03bda-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="03bda-284">Kod nie jest uruchamiany, chyba że `MonitoringEnabled` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="03bda-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="03bda-285">Wyprowadza bieżącą wartość `state` w jej `WriteConsole` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="03bda-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="03bda-286">Wystąpienie `ConfigurationMonitor` jest zarejestrowane jako usługa w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="03bda-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="03bda-287">Strona indeks zapewnia kontrolę nad konfiguracją przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03bda-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="03bda-288">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="03bda-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="03bda-289">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="03bda-290">Monitor konfiguracji ( `_monitor` ) służy do włączania lub wyłączania monitorowania oraz ustawiania bieżącego stanu dla opinii o interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="03bda-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="03bda-291">Gdy `OnPostStartMonitoring` jest wyzwalane, monitorowanie jest włączone, a bieżący stan jest wyczyszczony.</span><span class="sxs-lookup"><span data-stu-id="03bda-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="03bda-292">Gdy `OnPostStopMonitoring` jest wyzwalane, monitorowanie jest wyłączone, a stan jest ustawiony na odzwierciedlenie tego, że monitorowanie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="03bda-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="03bda-293">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="03bda-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="03bda-294">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="03bda-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="03bda-295">Monitoruj zmiany plików w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="03bda-295">Monitor cached file changes</span></span>

<span data-ttu-id="03bda-296">Zawartość pliku może być buforowana w pamięci za pomocą polecenia <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="03bda-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="03bda-297">Buforowanie w pamięci jest opisane w temacie [pamięć podręczna w pamięci podręcznej](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="03bda-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="03bda-298">Bez podejmowania dodatkowych czynności, takich jak implementacja opisana poniżej, *nieodświeżone (przestarzałe* ) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe zostaną zmienione.</span><span class="sxs-lookup"><span data-stu-id="03bda-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="03bda-299">Na przykład nie uwzględnia stanu pliku źródłowego w pamięci podręcznej podczas odnawiania przedziału czasu [wygaśnięcia](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="03bda-300">Każde żądanie dotyczące danych odnawia przedział czasu wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="03bda-301">Wszystkie funkcje aplikacji korzystające z zawartości w pamięci podręcznej są uzależnione od potencjalnie otrzymywania nieodświeżonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="03bda-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="03bda-302">Użycie tokenów zmiany w scenariuszu buforowania plików uniemożliwia obecność przestarzałych zawartości plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="03bda-303">Przykładowa aplikacja prezentuje implementację metody.</span><span class="sxs-lookup"><span data-stu-id="03bda-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="03bda-304">Przykład używa `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="03bda-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="03bda-305">Zwróć zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-305">Return file content.</span></span>
* <span data-ttu-id="03bda-306">Zaimplementuj algorytm ponawiania prób z wycofywaniem z powrotem, aby uwzględnić przypadki, w których blokada pliku tymczasowo uniemożliwia odczytywanie pliku.</span><span class="sxs-lookup"><span data-stu-id="03bda-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="03bda-307">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="03bda-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="03bda-308">`FileService`Jest tworzony w celu obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="03bda-309">`GetFileContent`Wywołanie metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci i zwrócić ją do obiektu wywołującego (*Services/FileService. cs*).</span><span class="sxs-lookup"><span data-stu-id="03bda-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="03bda-310">Jeśli buforowana zawartość nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną wykonane następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="03bda-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="03bda-311">Zawartość pliku jest uzyskiwana przy użyciu `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="03bda-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="03bda-312">Token zmiany jest uzyskiwany od dostawcy plików z [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="03bda-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="03bda-313">Wywołanie zwrotne tokenu jest wyzwalane, gdy plik zostanie zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="03bda-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="03bda-314">Zawartość pliku jest buforowana z [ruchomym](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) okresem ważności.</span><span class="sxs-lookup"><span data-stu-id="03bda-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="03bda-315">Token zmiany jest dołączony do [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) w celu wykluczenia wpisu pamięci podręcznej, jeśli plik zostanie zmieniony w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="03bda-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="03bda-316">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="03bda-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="03bda-317">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> wskazania wskazujące na aplikację <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="03bda-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="03bda-318">`filePath`Jest on uzyskiwany za pomocą [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="03bda-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="03bda-319">`FileService`Program jest zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="03bda-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="03bda-320">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="03bda-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="03bda-321">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="03bda-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="03bda-322">Na stronie indeksu `OnGet` (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="03bda-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="03bda-323">Klasa CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="03bda-323">CompositeChangeToken class</span></span>

<span data-ttu-id="03bda-324">Dla reprezentowania jednego lub większej liczby `IChangeToken` wystąpień w pojedynczym obiekcie, użyj <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> klasy.</span><span class="sxs-lookup"><span data-stu-id="03bda-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="03bda-325">`HasChanged`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `HasChanged` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="03bda-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="03bda-326">`ActiveChangeCallbacks`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `ActiveChangeCallbacks` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="03bda-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="03bda-327">W przypadku wystąpienia wielu współbieżnych zdarzeń zmiany wywołanie zwrotne zmiany złożonego jest wywoływana jednokrotnie.</span><span class="sxs-lookup"><span data-stu-id="03bda-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="03bda-328">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="03bda-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
