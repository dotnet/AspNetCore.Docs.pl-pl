---
title: Wykrywanie zmian przy użyciu tokenów zmiany w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak śledzić zmiany przy użyciu tokenów zmian.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: f52b210b33cd319e36570a1f731bb02a86189a96
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017483"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="b186e-103">Wykrywanie zmian przy użyciu tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b186e-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b186e-104">*Tokenem zmiany* jest ogólnym blokiem konstrukcyjnym, który służy do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="b186e-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="b186e-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b186e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="b186e-106">IChangeToken, interfejs</span><span class="sxs-lookup"><span data-stu-id="b186e-106">IChangeToken interface</span></span>

<span data-ttu-id="b186e-107"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="b186e-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="b186e-108">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b186e-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b186e-109">Pakiet NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b186e-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="b186e-110">`IChangeToken`ma dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="b186e-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="b186e-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskaż, czy token aktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="b186e-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="b186e-112">Jeśli `ActiveChangedCallbacks` jest ustawiona na `false` , wywołanie zwrotne nigdy nie zostanie wywołane, a aplikacja musi sondować `HasChanged` pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="b186e-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="b186e-113">Istnieje również możliwość, że token nigdy nie zostanie anulowany, jeśli nie wystąpią żadne zmiany lub zostanie usunięty lub wyłączony odbiornik zmian.</span><span class="sxs-lookup"><span data-stu-id="b186e-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="b186e-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>odbiera wartość wskazującą, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="b186e-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="b186e-115">`IChangeToken`Interfejs zawiera metodę [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , która rejestruje wywołanie zwrotne, które jest wywoływane, gdy token został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="b186e-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="b186e-116">`HasChanged`musi być ustawiona przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="b186e-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="b186e-117">Klasa ChangeToken</span><span class="sxs-lookup"><span data-stu-id="b186e-117">ChangeToken class</span></span>

<span data-ttu-id="b186e-118"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną służącą do propagowania powiadomień, w których wystąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="b186e-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="b186e-119">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b186e-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b186e-120">Pakiet NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) jest niejawnie dostarczany do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b186e-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="b186e-121">Metoda [ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywoływanie przy każdym zmianie tokenu:</span><span class="sxs-lookup"><span data-stu-id="b186e-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="b186e-122">`Func<IChangeToken>`tworzy token.</span><span class="sxs-lookup"><span data-stu-id="b186e-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="b186e-123">`Action`jest wywoływana, gdy zostanie zmieniony token.</span><span class="sxs-lookup"><span data-stu-id="b186e-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="b186e-124">Przeciążenie [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest przesyłany do odbiorcy tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="b186e-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="b186e-125">`OnChange`Zwraca wartość <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="b186e-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="b186e-126">Wywołanie <xref:System.IDisposable.Dispose*> uniemożliwia wysłuchanie tokenu w celu wprowadzenia dalszych zmian i zwolnienia zasobów tokenu.</span><span class="sxs-lookup"><span data-stu-id="b186e-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="b186e-127">Przykładowe zastosowania tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b186e-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="b186e-128">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian obiektów:</span><span class="sxs-lookup"><span data-stu-id="b186e-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="b186e-129">W przypadku monitorowania zmian w plikach <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> Metoda tworzy `IChangeToken` dla określonych plików lub folderów, które mają być monitorowane.</span><span class="sxs-lookup"><span data-stu-id="b186e-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="b186e-130">`IChangeToken`można dodać tokeny do wpisów pamięci podręcznej, aby wyzwolić wykluczenia z pamięci podręcznej w przypadku zmiany.</span><span class="sxs-lookup"><span data-stu-id="b186e-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="b186e-131">W przypadku `TOptions` zmian Domyślna <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma Przeciążenie, które akceptuje co najmniej jedno <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="b186e-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="b186e-132">Każde wystąpienie zwraca wartość, `IChangeToken` Aby zarejestrować zmiany zwrotne powiadomień o zmianach opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="b186e-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="b186e-133">Monitorowanie zmian konfiguracji</span><span class="sxs-lookup"><span data-stu-id="b186e-133">Monitor for configuration changes</span></span>

<span data-ttu-id="b186e-134">Domyślnie szablony ASP.NET Core używają [plików konfiguracji JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*i *appsettings.Production.json*) w celu załadowania ustawień konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b186e-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="b186e-135">Te pliki są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="b186e-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="b186e-136">`reloadOnChange`wskazuje, czy należy ponownie załadować konfigurację w przypadku zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="b186e-137">To ustawienie jest dostępne w <xref:Microsoft.Extensions.Hosting.Host> metodzie wygodnej <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="b186e-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="b186e-138">Konfiguracja oparta na plikach jest reprezentowana przez <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="b186e-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="b186e-139">`FileConfigurationSource`program używa <xref:Microsoft.Extensions.FileProviders.IFileProvider> do monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="b186e-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="b186e-140">Domyślnie `IFileMonitor` jest on dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , który służy <xref:System.IO.FileSystemWatcher> do monitorowania zmian w pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b186e-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="b186e-141">Przykładowa aplikacja przedstawia dwie implementacje monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b186e-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="b186e-142">Jeśli którykolwiek z plików *AppSettings* zostanie zmieniony, obydwie implementacje monitorowania plików wykonują kod niestandardowy, &mdash; a Przykładowa aplikacja zapisuje komunikat w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="b186e-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="b186e-143">Plik konfiguracji `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenów dla jednej zmiany pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b186e-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="b186e-144">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz w przypadku wyzwolenia wielu wywołań zwrotnych tokenów, implementacja próbki sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="b186e-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="b186e-145">W przykładzie zastosowano mieszanie plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="b186e-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="b186e-146">Ponowna próba jest zaimplementowana z wycofywaniem wykładniczym.</span><span class="sxs-lookup"><span data-stu-id="b186e-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="b186e-147">Ponowienie próby jest możliwe, ponieważ może wystąpić zablokowanie pliku, który tymczasowo uniemożliwia Obliczanie nowego skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="b186e-148">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="b186e-149">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b186e-149">Simple startup change token</span></span>

<span data-ttu-id="b186e-150">Zarejestruj `Action` wywołanie zwrotne odbiorcy tokenu dla powiadomień o zmianach w tokenie Załaduj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="b186e-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="b186e-151">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b186e-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="b186e-152">`config.GetReloadToken()`udostępnia token.</span><span class="sxs-lookup"><span data-stu-id="b186e-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="b186e-153">Wywołanie zwrotne to `InvokeChanged` Metoda:</span><span class="sxs-lookup"><span data-stu-id="b186e-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="b186e-154">`state`Wywołanie zwrotne jest używane do przekazywania w `IWebHostEnvironment` , co jest przydatne do określenia poprawnego pliku konfiguracyjnego *AppSettings* do monitorowania (na przykład *appsettings.Development.js* w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="b186e-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="b186e-155">Skróty plików są używane, aby zapobiec `WriteConsole` uruchamianiu instrukcji wiele razy z powodu wywołania zwrotnego wielu tokenów, gdy plik konfiguracyjny został zmieniony tylko raz.</span><span class="sxs-lookup"><span data-stu-id="b186e-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="b186e-156">Ten system działa tak długo, jak działa aplikacja i nie może zostać wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b186e-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="b186e-157">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="b186e-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="b186e-158">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="b186e-158">The sample implements:</span></span>

* <span data-ttu-id="b186e-159">Podstawowe monitorowanie tokenów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="b186e-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="b186e-160">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="b186e-160">Monitoring as a service.</span></span>
* <span data-ttu-id="b186e-161">Mechanizm do włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="b186e-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="b186e-162">Przykład ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="b186e-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="b186e-163">*Rozszerzenia/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="b186e-164">Konstruktor zaimplementowanej klasy, `ConfigurationMonitor` rejestruje wywołanie zwrotne dla powiadomień o zmianach:</span><span class="sxs-lookup"><span data-stu-id="b186e-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="b186e-165">`config.GetReloadToken()`dostarcza token.</span><span class="sxs-lookup"><span data-stu-id="b186e-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="b186e-166">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="b186e-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="b186e-167">`state`W tym wystąpieniu jest odwołanie do `IConfigurationMonitor` wystąpienia, które jest używane w celu uzyskania dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="b186e-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="b186e-168">Są używane dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="b186e-168">Two properties are used:</span></span>

* <span data-ttu-id="b186e-169">`MonitoringEnabled`: Wskazuje, czy wywołanie zwrotne powinno uruchamiać swój kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="b186e-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="b186e-170">`CurrentState`: Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b186e-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="b186e-171">`InvokeChanged`Metoda jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="b186e-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="b186e-172">Kod nie jest uruchamiany, chyba że `MonitoringEnabled` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="b186e-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="b186e-173">Wyprowadza bieżącą wartość `state` w jej `WriteConsole` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="b186e-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="b186e-174">Wystąpienie `ConfigurationMonitor` jest zarejestrowane jako usługa w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b186e-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="b186e-175">Strona indeks zapewnia kontrolę nad konfiguracją przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b186e-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="b186e-176">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="b186e-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="b186e-177">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b186e-178">Monitor konfiguracji ( `_monitor` ) służy do włączania lub wyłączania monitorowania oraz ustawiania bieżącego stanu dla opinii o interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="b186e-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="b186e-179">Gdy `OnPostStartMonitoring` jest wyzwalane, monitorowanie jest włączone, a bieżący stan jest wyczyszczony.</span><span class="sxs-lookup"><span data-stu-id="b186e-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="b186e-180">Gdy `OnPostStopMonitoring` jest wyzwalane, monitorowanie jest wyłączone, a stan jest ustawiony na odzwierciedlenie tego, że monitorowanie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="b186e-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="b186e-181">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="b186e-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="b186e-182">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b186e-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="b186e-183">Monitoruj zmiany plików w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b186e-183">Monitor cached file changes</span></span>

<span data-ttu-id="b186e-184">Zawartość pliku może być buforowana w pamięci za pomocą polecenia <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="b186e-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="b186e-185">Buforowanie w pamięci jest opisane w temacie [pamięć podręczna w pamięci podręcznej](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="b186e-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="b186e-186">Bez podejmowania dodatkowych czynności, takich jak implementacja opisana poniżej, *nieodświeżone (przestarzałe* ) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe zostaną zmienione.</span><span class="sxs-lookup"><span data-stu-id="b186e-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="b186e-187">Na przykład nie uwzględnia stanu pliku źródłowego w pamięci podręcznej podczas odnawiania przedziału czasu [wygaśnięcia](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="b186e-188">Każde żądanie dotyczące danych odnawia przedział czasu wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="b186e-189">Wszystkie funkcje aplikacji korzystające z zawartości w pamięci podręcznej są uzależnione od potencjalnie otrzymywania nieodświeżonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="b186e-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="b186e-190">Użycie tokenów zmiany w scenariuszu buforowania plików uniemożliwia obecność przestarzałych zawartości plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="b186e-191">Przykładowa aplikacja prezentuje implementację metody.</span><span class="sxs-lookup"><span data-stu-id="b186e-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="b186e-192">Przykład używa `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="b186e-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="b186e-193">Zwróć zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-193">Return file content.</span></span>
* <span data-ttu-id="b186e-194">Zaimplementuj algorytm ponawiania prób z wycofywaniem z powrotem, aby uwzględnić przypadki, w których blokada pliku tymczasowo uniemożliwia odczytywanie pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="b186e-195">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="b186e-196">`FileService`Jest tworzony w celu obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="b186e-197">`GetFileContent`Wywołanie metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci i zwrócić ją do obiektu wywołującego (*Services/FileService. cs*).</span><span class="sxs-lookup"><span data-stu-id="b186e-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="b186e-198">Jeśli buforowana zawartość nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną wykonane następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="b186e-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="b186e-199">Zawartość pliku jest uzyskiwana przy użyciu `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="b186e-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="b186e-200">Token zmiany jest uzyskiwany od dostawcy plików z [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="b186e-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="b186e-201">Wywołanie zwrotne tokenu jest wyzwalane, gdy plik zostanie zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="b186e-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="b186e-202">Zawartość pliku jest buforowana z [ruchomym](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) okresem ważności.</span><span class="sxs-lookup"><span data-stu-id="b186e-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="b186e-203">Token zmiany jest dołączony do [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) w celu wykluczenia wpisu pamięci podręcznej, jeśli plik zostanie zmieniony w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="b186e-204">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b186e-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="b186e-205">`IWebHostEnvironment.ContentRootFileProvider`służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> wskazujący na aplikację `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="b186e-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="b186e-206">`filePath`Jest on uzyskiwany za pomocą [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="b186e-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="b186e-207">`FileService`Program jest zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="b186e-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="b186e-208">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b186e-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="b186e-209">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="b186e-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="b186e-210">Na stronie indeksu `OnGet` (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b186e-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="b186e-211">Klasa CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="b186e-211">CompositeChangeToken class</span></span>

<span data-ttu-id="b186e-212">Dla reprezentowania jednego lub większej liczby `IChangeToken` wystąpień w pojedynczym obiekcie, użyj <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> klasy.</span><span class="sxs-lookup"><span data-stu-id="b186e-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="b186e-213">`HasChanged`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `HasChanged` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="b186e-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="b186e-214">`ActiveChangeCallbacks`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `ActiveChangeCallbacks` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="b186e-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="b186e-215">W przypadku wystąpienia wielu współbieżnych zdarzeń zmiany wywołanie zwrotne zmiany złożonego jest wywoływana jednokrotnie.</span><span class="sxs-lookup"><span data-stu-id="b186e-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b186e-216">*Tokenem zmiany* jest ogólnym blokiem konstrukcyjnym, który służy do śledzenia zmian stanu.</span><span class="sxs-lookup"><span data-stu-id="b186e-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="b186e-217">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b186e-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="b186e-218">IChangeToken, interfejs</span><span class="sxs-lookup"><span data-stu-id="b186e-218">IChangeToken interface</span></span>

<span data-ttu-id="b186e-219"><xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="b186e-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="b186e-220">`IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b186e-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b186e-221">W przypadku aplikacji, które nie korzystają z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu dla pakietu NuGet [Microsoft. Extensions. pierwotne](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="b186e-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="b186e-222">`IChangeToken`ma dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="b186e-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="b186e-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskaż, czy token aktywnie wywołuje wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="b186e-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="b186e-224">Jeśli `ActiveChangedCallbacks` jest ustawiona na `false` , wywołanie zwrotne nigdy nie zostanie wywołane, a aplikacja musi sondować `HasChanged` pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="b186e-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="b186e-225">Istnieje również możliwość, że token nigdy nie zostanie anulowany, jeśli nie wystąpią żadne zmiany lub zostanie usunięty lub wyłączony odbiornik zmian.</span><span class="sxs-lookup"><span data-stu-id="b186e-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="b186e-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>odbiera wartość wskazującą, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="b186e-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="b186e-227">`IChangeToken`Interfejs zawiera metodę [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , która rejestruje wywołanie zwrotne, które jest wywoływane, gdy token został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="b186e-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="b186e-228">`HasChanged`musi być ustawiona przed wywołaniem wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="b186e-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="b186e-229">Klasa ChangeToken</span><span class="sxs-lookup"><span data-stu-id="b186e-229">ChangeToken class</span></span>

<span data-ttu-id="b186e-230"><xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną służącą do propagowania powiadomień, w których wystąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="b186e-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="b186e-231">`ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b186e-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b186e-232">W przypadku aplikacji, które nie korzystają z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu dla pakietu NuGet [Microsoft. Extensions. pierwotne](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="b186e-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="b186e-233">Metoda [ChangeToken. OnChange (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywoływanie przy każdym zmianie tokenu:</span><span class="sxs-lookup"><span data-stu-id="b186e-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="b186e-234">`Func<IChangeToken>`tworzy token.</span><span class="sxs-lookup"><span data-stu-id="b186e-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="b186e-235">`Action`jest wywoływana, gdy zostanie zmieniony token.</span><span class="sxs-lookup"><span data-stu-id="b186e-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="b186e-236">Przeciążenie [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest przesyłany do odbiorcy tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="b186e-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="b186e-237">`OnChange`Zwraca wartość <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="b186e-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="b186e-238">Wywołanie <xref:System.IDisposable.Dispose*> uniemożliwia wysłuchanie tokenu w celu wprowadzenia dalszych zmian i zwolnienia zasobów tokenu.</span><span class="sxs-lookup"><span data-stu-id="b186e-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="b186e-239">Przykładowe zastosowania tokenów zmiany w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b186e-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="b186e-240">Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian obiektów:</span><span class="sxs-lookup"><span data-stu-id="b186e-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="b186e-241">W przypadku monitorowania zmian w plikach <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> Metoda tworzy `IChangeToken` dla określonych plików lub folderów, które mają być monitorowane.</span><span class="sxs-lookup"><span data-stu-id="b186e-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="b186e-242">`IChangeToken`można dodać tokeny do wpisów pamięci podręcznej, aby wyzwolić wykluczenia z pamięci podręcznej w przypadku zmiany.</span><span class="sxs-lookup"><span data-stu-id="b186e-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="b186e-243">W przypadku `TOptions` zmian Domyślna <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma Przeciążenie, które akceptuje co najmniej jedno <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="b186e-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="b186e-244">Każde wystąpienie zwraca wartość, `IChangeToken` Aby zarejestrować zmiany zwrotne powiadomień o zmianach opcji śledzenia.</span><span class="sxs-lookup"><span data-stu-id="b186e-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="b186e-245">Monitorowanie zmian konfiguracji</span><span class="sxs-lookup"><span data-stu-id="b186e-245">Monitor for configuration changes</span></span>

<span data-ttu-id="b186e-246">Domyślnie szablony ASP.NET Core używają [plików konfiguracji JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*i *appsettings.Production.json*) w celu załadowania ustawień konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b186e-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="b186e-247">Te pliki są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="b186e-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="b186e-248">`reloadOnChange`wskazuje, czy należy ponownie załadować konfigurację w przypadku zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="b186e-249">To ustawienie jest dostępne w <xref:Microsoft.AspNetCore.WebHost> metodzie wygodnej <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="b186e-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="b186e-250">Konfiguracja oparta na plikach jest reprezentowana przez <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="b186e-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="b186e-251">`FileConfigurationSource`program używa <xref:Microsoft.Extensions.FileProviders.IFileProvider> do monitorowania plików.</span><span class="sxs-lookup"><span data-stu-id="b186e-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="b186e-252">Domyślnie `IFileMonitor` jest on dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , który służy <xref:System.IO.FileSystemWatcher> do monitorowania zmian w pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b186e-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="b186e-253">Przykładowa aplikacja przedstawia dwie implementacje monitorowania zmian konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b186e-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="b186e-254">Jeśli którykolwiek z plików *AppSettings* zostanie zmieniony, obydwie implementacje monitorowania plików wykonują kod niestandardowy, &mdash; a Przykładowa aplikacja zapisuje komunikat w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="b186e-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="b186e-255">Plik konfiguracji `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenów dla jednej zmiany pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b186e-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="b186e-256">Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz w przypadku wyzwolenia wielu wywołań zwrotnych tokenów, implementacja próbki sprawdza skróty plików.</span><span class="sxs-lookup"><span data-stu-id="b186e-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="b186e-257">W przykładzie zastosowano mieszanie plików SHA1.</span><span class="sxs-lookup"><span data-stu-id="b186e-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="b186e-258">Ponowna próba jest zaimplementowana z wycofywaniem wykładniczym.</span><span class="sxs-lookup"><span data-stu-id="b186e-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="b186e-259">Ponowienie próby jest możliwe, ponieważ może wystąpić zablokowanie pliku, który tymczasowo uniemożliwia Obliczanie nowego skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="b186e-260">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="b186e-261">Prosty token zmiany uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b186e-261">Simple startup change token</span></span>

<span data-ttu-id="b186e-262">Zarejestruj `Action` wywołanie zwrotne odbiorcy tokenu dla powiadomień o zmianach w tokenie Załaduj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="b186e-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="b186e-263">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b186e-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="b186e-264">`config.GetReloadToken()`udostępnia token.</span><span class="sxs-lookup"><span data-stu-id="b186e-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="b186e-265">Wywołanie zwrotne to `InvokeChanged` Metoda:</span><span class="sxs-lookup"><span data-stu-id="b186e-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="b186e-266">`state`Wywołanie zwrotne jest używane do przekazywania w `IHostingEnvironment` , co jest przydatne do określenia poprawnego pliku konfiguracyjnego *AppSettings* do monitorowania (na przykład *appsettings.Development.js* w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="b186e-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="b186e-267">Skróty plików są używane, aby zapobiec `WriteConsole` uruchamianiu instrukcji wiele razy z powodu wywołania zwrotnego wielu tokenów, gdy plik konfiguracyjny został zmieniony tylko raz.</span><span class="sxs-lookup"><span data-stu-id="b186e-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="b186e-268">Ten system działa tak długo, jak działa aplikacja i nie może zostać wyłączona przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b186e-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="b186e-269">Monitorowanie zmian konfiguracji jako usługi</span><span class="sxs-lookup"><span data-stu-id="b186e-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="b186e-270">Przykład implementuje:</span><span class="sxs-lookup"><span data-stu-id="b186e-270">The sample implements:</span></span>

* <span data-ttu-id="b186e-271">Podstawowe monitorowanie tokenów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="b186e-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="b186e-272">Monitorowanie jako usługa.</span><span class="sxs-lookup"><span data-stu-id="b186e-272">Monitoring as a service.</span></span>
* <span data-ttu-id="b186e-273">Mechanizm do włączania i wyłączania monitorowania.</span><span class="sxs-lookup"><span data-stu-id="b186e-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="b186e-274">Przykład ustanawia `IConfigurationMonitor` interfejs.</span><span class="sxs-lookup"><span data-stu-id="b186e-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="b186e-275">*Rozszerzenia/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="b186e-276">Konstruktor zaimplementowanej klasy, `ConfigurationMonitor` rejestruje wywołanie zwrotne dla powiadomień o zmianach:</span><span class="sxs-lookup"><span data-stu-id="b186e-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="b186e-277">`config.GetReloadToken()`dostarcza token.</span><span class="sxs-lookup"><span data-stu-id="b186e-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="b186e-278">`InvokeChanged`jest metodą wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="b186e-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="b186e-279">`state`W tym wystąpieniu jest odwołanie do `IConfigurationMonitor` wystąpienia, które jest używane w celu uzyskania dostępu do stanu monitorowania.</span><span class="sxs-lookup"><span data-stu-id="b186e-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="b186e-280">Są używane dwie właściwości:</span><span class="sxs-lookup"><span data-stu-id="b186e-280">Two properties are used:</span></span>

* <span data-ttu-id="b186e-281">`MonitoringEnabled`: Wskazuje, czy wywołanie zwrotne powinno uruchamiać swój kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="b186e-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="b186e-282">`CurrentState`: Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b186e-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="b186e-283">`InvokeChanged`Metoda jest podobna do wcześniejszego podejścia, z tą różnicą, że:</span><span class="sxs-lookup"><span data-stu-id="b186e-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="b186e-284">Kod nie jest uruchamiany, chyba że `MonitoringEnabled` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="b186e-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="b186e-285">Wyprowadza bieżącą wartość `state` w jej `WriteConsole` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="b186e-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="b186e-286">Wystąpienie `ConfigurationMonitor` jest zarejestrowane jako usługa w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b186e-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="b186e-287">Strona indeks zapewnia kontrolę nad konfiguracją przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b186e-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="b186e-288">Wystąpienie `IConfigurationMonitor` jest wstrzykiwane do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="b186e-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="b186e-289">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b186e-290">Monitor konfiguracji ( `_monitor` ) służy do włączania lub wyłączania monitorowania oraz ustawiania bieżącego stanu dla opinii o interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="b186e-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="b186e-291">Gdy `OnPostStartMonitoring` jest wyzwalane, monitorowanie jest włączone, a bieżący stan jest wyczyszczony.</span><span class="sxs-lookup"><span data-stu-id="b186e-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="b186e-292">Gdy `OnPostStopMonitoring` jest wyzwalane, monitorowanie jest wyłączone, a stan jest ustawiony na odzwierciedlenie tego, że monitorowanie nie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="b186e-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="b186e-293">Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.</span><span class="sxs-lookup"><span data-stu-id="b186e-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="b186e-294">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b186e-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="b186e-295">Monitoruj zmiany plików w pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="b186e-295">Monitor cached file changes</span></span>

<span data-ttu-id="b186e-296">Zawartość pliku może być buforowana w pamięci za pomocą polecenia <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="b186e-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="b186e-297">Buforowanie w pamięci jest opisane w temacie [pamięć podręczna w pamięci podręcznej](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="b186e-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="b186e-298">Bez podejmowania dodatkowych czynności, takich jak implementacja opisana poniżej, *nieodświeżone (przestarzałe* ) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe zostaną zmienione.</span><span class="sxs-lookup"><span data-stu-id="b186e-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="b186e-299">Na przykład nie uwzględnia stanu pliku źródłowego w pamięci podręcznej podczas odnawiania przedziału czasu [wygaśnięcia](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="b186e-300">Każde żądanie dotyczące danych odnawia przedział czasu wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="b186e-301">Wszystkie funkcje aplikacji korzystające z zawartości w pamięci podręcznej są uzależnione od potencjalnie otrzymywania nieodświeżonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="b186e-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="b186e-302">Użycie tokenów zmiany w scenariuszu buforowania plików uniemożliwia obecność przestarzałych zawartości plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="b186e-303">Przykładowa aplikacja prezentuje implementację metody.</span><span class="sxs-lookup"><span data-stu-id="b186e-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="b186e-304">Przykład używa `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="b186e-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="b186e-305">Zwróć zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-305">Return file content.</span></span>
* <span data-ttu-id="b186e-306">Zaimplementuj algorytm ponawiania prób z wycofywaniem z powrotem, aby uwzględnić przypadki, w których blokada pliku tymczasowo uniemożliwia odczytywanie pliku.</span><span class="sxs-lookup"><span data-stu-id="b186e-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="b186e-307">*Narzędzia/Narzędzia. cs*:</span><span class="sxs-lookup"><span data-stu-id="b186e-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="b186e-308">`FileService`Jest tworzony w celu obsługi wyszukiwania plików w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="b186e-309">`GetFileContent`Wywołanie metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci i zwrócić ją do obiektu wywołującego (*Services/FileService. cs*).</span><span class="sxs-lookup"><span data-stu-id="b186e-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="b186e-310">Jeśli buforowana zawartość nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną wykonane następujące akcje:</span><span class="sxs-lookup"><span data-stu-id="b186e-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="b186e-311">Zawartość pliku jest uzyskiwana przy użyciu `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="b186e-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="b186e-312">Token zmiany jest uzyskiwany od dostawcy plików z [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="b186e-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="b186e-313">Wywołanie zwrotne tokenu jest wyzwalane, gdy plik zostanie zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="b186e-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="b186e-314">Zawartość pliku jest buforowana z [ruchomym](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) okresem ważności.</span><span class="sxs-lookup"><span data-stu-id="b186e-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="b186e-315">Token zmiany jest dołączony do [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) w celu wykluczenia wpisu pamięci podręcznej, jeśli plik zostanie zmieniony w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="b186e-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="b186e-316">W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b186e-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="b186e-317">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> wskazania wskazujące na aplikację <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="b186e-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="b186e-318">`filePath`Jest on uzyskiwany za pomocą [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="b186e-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="b186e-319">`FileService`Program jest zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.</span><span class="sxs-lookup"><span data-stu-id="b186e-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="b186e-320">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b186e-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="b186e-321">Model strony ładuje zawartość pliku przy użyciu usługi.</span><span class="sxs-lookup"><span data-stu-id="b186e-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="b186e-322">Na stronie indeksu `OnGet` (*strony/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="b186e-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="b186e-323">Klasa CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="b186e-323">CompositeChangeToken class</span></span>

<span data-ttu-id="b186e-324">Dla reprezentowania jednego lub większej liczby `IChangeToken` wystąpień w pojedynczym obiekcie, użyj <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> klasy.</span><span class="sxs-lookup"><span data-stu-id="b186e-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="b186e-325">`HasChanged`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `HasChanged` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="b186e-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="b186e-326">`ActiveChangeCallbacks`w przypadku raportowania tokenu złożonego, `true` Jeśli jakikolwiek reprezentowany token `ActiveChangeCallbacks` jest `true` .</span><span class="sxs-lookup"><span data-stu-id="b186e-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="b186e-327">W przypadku wystąpienia wielu współbieżnych zdarzeń zmiany wywołanie zwrotne zmiany złożonego jest wywoływana jednokrotnie.</span><span class="sxs-lookup"><span data-stu-id="b186e-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b186e-328">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b186e-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
