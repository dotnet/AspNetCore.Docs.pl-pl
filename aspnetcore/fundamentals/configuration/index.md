---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core przy użyciu interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 7715adc9b39edd4f8a5882b2e60a1b5513fe400b
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205998"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="72d07-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72d07-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="72d07-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="72d07-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="72d07-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="72d07-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="72d07-106">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="72d07-107">Pliki ustawień, takie jak *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="72d07-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="72d07-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="72d07-108">Environment variables</span></span>
* <span data-ttu-id="72d07-109">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d07-109">Azure Key Vault</span></span>
* <span data-ttu-id="72d07-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="72d07-110">Azure App Configuration</span></span>
* <span data-ttu-id="72d07-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-111">Command-line arguments</span></span>
* <span data-ttu-id="72d07-112">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="72d07-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="72d07-113">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="72d07-113">Directory files</span></span>
* <span data-ttu-id="72d07-114">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="72d07-114">In-memory .NET objects</span></span>

<span data-ttu-id="72d07-115">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72d07-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="72d07-116">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="72d07-116">Default configuration</span></span>

<span data-ttu-id="72d07-117">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="72d07-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="72d07-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="72d07-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="72d07-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący `IConfiguration` element jako źródło.</span><span class="sxs-lookup"><span data-stu-id="72d07-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="72d07-120">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="72d07-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="72d07-121">[appSettings. JSON](#appsettingsjson) przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="72d07-122">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="72d07-123">Na przykład *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. *kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="72d07-124">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest `Development` uruchamiana w środowisku.</span><span class="sxs-lookup"><span data-stu-id="72d07-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="72d07-125">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="72d07-126">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="72d07-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="72d07-127">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="72d07-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="72d07-128">Na przykład, jeśli `MyKey` jest ustawiony zarówno w pliku *appSettings. JSON* , jak i w środowisku, zostanie użyta wartość środowiska.</span><span class="sxs-lookup"><span data-stu-id="72d07-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="72d07-129">Przy użyciu domyślnych dostawców konfiguracji [dostawca konfiguracji wiersza polecenia](#command-line-configuration-provider) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="72d07-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="72d07-130">Aby uzyskać więcej informacji `CreateDefaultBuilder`na temat, zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="72d07-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="72d07-131">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="72d07-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="72d07-132">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="72d07-132">appsettings.json</span></span>

<span data-ttu-id="72d07-133">Rozważmy następujący plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="72d07-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="72d07-134">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-135">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="72d07-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="72d07-136">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="72d07-136">*appsettings.json*</span></span>
1. <span data-ttu-id="72d07-137">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d07-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="72d07-138">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="72d07-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="72d07-139">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="72d07-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="72d07-140">*AppSettings*. `Environment`. wartości *JSON* przesłaniają klucze w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="72d07-141">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="72d07-141">For example, by default:</span></span>

* <span data-ttu-id="72d07-142">W obszarze programowanie, *AppSettings*. ***Programowanie***. Konfiguracja *JSON* zastępuje wartości Znalezione w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="72d07-143">W obszarze produkcja, *AppSettings*. ***Środowisko produkcyjne***. Konfiguracja *JSON* zastępuje wartości Znalezione w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="72d07-144">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="72d07-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="72d07-145">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="72d07-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="72d07-146">Preferowanym sposobem odczytywania pokrewnych wartości konfiguracji jest użycie [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="72d07-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="72d07-147">Na przykład, aby odczytać następujące wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="72d07-148">Utwórz następującą `PositionOptions` klasę:</span><span class="sxs-lookup"><span data-stu-id="72d07-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="72d07-149">Wszystkie publiczne właściwości odczytu i zapisu typu są powiązane.</span><span class="sxs-lookup"><span data-stu-id="72d07-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="72d07-150">Pola ***nie*** są powiązane.</span><span class="sxs-lookup"><span data-stu-id="72d07-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="72d07-151">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="72d07-151">The following code:</span></span>

* <span data-ttu-id="72d07-152">Wywołuje [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , `PositionOptions` aby powiązać klasę z `Position` sekcją.</span><span class="sxs-lookup"><span data-stu-id="72d07-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="72d07-153">Wyświetla dane `Position` konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="72d07-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="72d07-155">`ConfigurationBinder.Get<T>`może być wygodniejsze niż używanie `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="72d07-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="72d07-156">Poniższy kod przedstawia sposób użycia `ConfigurationBinder.Get<T>` z `PositionOptions` klasą:</span><span class="sxs-lookup"><span data-stu-id="72d07-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-157">Alternatywne podejście w przypadku używania ***wzorca opcji*** ma na celu powiązanie `Position` sekcji i dodanie jej do [kontenera usługi iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="72d07-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="72d07-158">W poniższym kodzie `PositionOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="72d07-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="72d07-159">Korzystając z powyższego kodu, poniższy kod odczytuje opcje pozycji:</span><span class="sxs-lookup"><span data-stu-id="72d07-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-160">Korzystając z konfiguracji [domyślnej](#default) , pliku *appSettings. JSON* i *appSettings.* `Environment`pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="72d07-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="72d07-161">Zmiany wprowadzone w pliku *appSettings. JSON* i *appSettings.* `Environment`plik *. JSON* ***po*** uruchomieniu aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="72d07-162">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="72d07-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="72d07-163">Security and Secret Manager</span><span class="sxs-lookup"><span data-stu-id="72d07-163">Security and secret manager</span></span>

<span data-ttu-id="72d07-164">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="72d07-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="72d07-165">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="72d07-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="72d07-166">Za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="72d07-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="72d07-167">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="72d07-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="72d07-168">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="72d07-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="72d07-169">[Domyślnie](#default)program [Secret Manager](xref:security/app-secrets) odczytuje ustawienia konfiguracji po pliku *appSettings. JSON* i *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="72d07-170">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="72d07-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="72d07-171"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="72d07-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="72d07-172">Menedżer wpisów tajnych używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="72d07-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="72d07-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="72d07-174">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d07-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="72d07-175">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="72d07-175">Environment variables</span></span>

<span data-ttu-id="72d07-176">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość po odczytywaniu pliku *appSettings. JSON*, *appSettings.* `Environment` *. JSON*i [Menedżer wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="72d07-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="72d07-177">W związku z tym kluczowe wartości odczytywane z wartości zastąpienia środowiska są odczytywane z pliku *appSettings. JSON*, *appSettings.* `Environment` *. JSON*i Menedżer wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="72d07-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="72d07-178">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="72d07-178">The following `set` commands:</span></span>

* <span data-ttu-id="72d07-179">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="72d07-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="72d07-180">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="72d07-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="72d07-181">`dotnet run` Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="72d07-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="72d07-182">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="72d07-182">The preceding environment settings:</span></span>

* <span data-ttu-id="72d07-183">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="72d07-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="72d07-184">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="72d07-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="72d07-185">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="72d07-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="72d07-186">W `set`przeciwieństwie `setx` do, ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="72d07-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="72d07-187">`/M`ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="72d07-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="72d07-188">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="72d07-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="72d07-189">Aby sprawdzić, czy poprzednie polecenia przesłaniają plik *appSettings. JSON* i *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72d07-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="72d07-190">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="72d07-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="72d07-191">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i `dotnet run`wprowadź.</span><span class="sxs-lookup"><span data-stu-id="72d07-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="72d07-192">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="72d07-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="72d07-193">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-193">In the preceding code:</span></span>

* <span data-ttu-id="72d07-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="72d07-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="72d07-195">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="72d07-196">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="72d07-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="72d07-197">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="72d07-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="72d07-198">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="72d07-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="72d07-199">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="72d07-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="72d07-200">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="72d07-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="72d07-201">`DOTNET_` Prefiksy `ASPNETCORE_` i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="72d07-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="72d07-202">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="72d07-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="72d07-203">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="72d07-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="72d07-204">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="72d07-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="72d07-205">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="72d07-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="72d07-206">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="72d07-206">Exposed as environment variables.</span></span>

<span data-ttu-id="72d07-207">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="72d07-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="72d07-208">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="72d07-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="72d07-209">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-209">Command-line</span></span>

<span data-ttu-id="72d07-210">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="72d07-211">*appSettings. JSON* i *AppSettings*. `Environment`. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d07-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="72d07-212">Wpisy [tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="72d07-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d07-213">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="72d07-213">Environment variables.</span></span>

<span data-ttu-id="72d07-214">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="72d07-215">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-215">Command-line arguments</span></span>

<span data-ttu-id="72d07-216">Następujące polecenie ustawia klucze i wartości przy użyciu `=`:</span><span class="sxs-lookup"><span data-stu-id="72d07-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="72d07-217">Następujące polecenie ustawia klucze i wartości przy użyciu `/`:</span><span class="sxs-lookup"><span data-stu-id="72d07-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="72d07-218">Następujące polecenie ustawia klucze i wartości przy użyciu `--`:</span><span class="sxs-lookup"><span data-stu-id="72d07-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="72d07-219">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="72d07-219">The key value:</span></span>

* <span data-ttu-id="72d07-220">Musi być `=`zgodna lub musi mieć prefiks `--` lub `/` , gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="72d07-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="72d07-221">Nie jest wymagane `=` , jeśli jest używany.</span><span class="sxs-lookup"><span data-stu-id="72d07-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="72d07-222">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="72d07-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="72d07-223">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które `=` są używane z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="72d07-224">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="72d07-224">Switch mappings</span></span>

<span data-ttu-id="72d07-225">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="72d07-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="72d07-226">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="72d07-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="72d07-227">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="72d07-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="72d07-228">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="72d07-229">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską (`-`).</span><span class="sxs-lookup"><span data-stu-id="72d07-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="72d07-230">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="72d07-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="72d07-231">Przełączniki muszą zaczynać `-` się `--`od lub.</span><span class="sxs-lookup"><span data-stu-id="72d07-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="72d07-232">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="72d07-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="72d07-233">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="72d07-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="72d07-234">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="72d07-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-235">Uruchom następujące polecenie, aby przetestować zastąpienie klucza:</span><span class="sxs-lookup"><span data-stu-id="72d07-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="72d07-236">Uwaga: obecnie nie `=` można użyć, aby ustawić wartości zastępcze klucza za pomocą pojedynczej `-`kreski.</span><span class="sxs-lookup"><span data-stu-id="72d07-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="72d07-237">Zobacz [ten problem](https://github.com/dotnet/extensions/issues/3059)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="72d07-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="72d07-238">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="72d07-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="72d07-239">W przypadku aplikacji korzystających z mapowań przełączników wywołanie `CreateDefaultBuilder` nie powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="72d07-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="72d07-240">Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="72d07-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d07-241">Rozwiązanie nie przekazuje argumentów do `CreateDefaultBuilder` , ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="72d07-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="72d07-242">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-242">Hierarchical configuration data</span></span>

<span data-ttu-id="72d07-243">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="72d07-244">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="72d07-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="72d07-245">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-246">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="72d07-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="72d07-247">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="72d07-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="72d07-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>metody <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="72d07-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="72d07-249">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="72d07-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="72d07-250">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-250">Configuration keys and values</span></span>

<span data-ttu-id="72d07-251">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-251">Configuration keys:</span></span>

* <span data-ttu-id="72d07-252">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="72d07-252">Are case-insensitive.</span></span> <span data-ttu-id="72d07-253">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="72d07-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="72d07-254">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="72d07-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="72d07-255">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="72d07-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="72d07-256">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="72d07-256">Hierarchical keys</span></span>
  * <span data-ttu-id="72d07-257">W interfejsie API konfiguracji, separator dwukropek`:`() działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="72d07-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="72d07-258">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="72d07-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="72d07-259">Podwójne podkreślenie, `__`,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:`.</span><span class="sxs-lookup"><span data-stu-id="72d07-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="72d07-260">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="72d07-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="72d07-261">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="72d07-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d07-263">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="72d07-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="72d07-264">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-264">Configuration values:</span></span>

* <span data-ttu-id="72d07-265">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="72d07-265">Are strings.</span></span>
* <span data-ttu-id="72d07-266">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="72d07-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="72d07-267">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-267">Configuration providers</span></span>

<span data-ttu-id="72d07-268">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="72d07-269">Dostawca</span><span class="sxs-lookup"><span data-stu-id="72d07-269">Provider</span></span> | <span data-ttu-id="72d07-270">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="72d07-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="72d07-271">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d07-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="72d07-272">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d07-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="72d07-273">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="72d07-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="72d07-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="72d07-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="72d07-275">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="72d07-276">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-276">Command-line parameters</span></span> |
| [<span data-ttu-id="72d07-277">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="72d07-278">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="72d07-278">Custom source</span></span> |
| [<span data-ttu-id="72d07-279">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="72d07-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="72d07-280">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="72d07-280">Environment variables</span></span> |
| [<span data-ttu-id="72d07-281">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="72d07-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="72d07-282">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="72d07-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="72d07-283">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="72d07-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="72d07-284">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="72d07-284">Directory files</span></span> |
| [<span data-ttu-id="72d07-285">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="72d07-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="72d07-286">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="72d07-286">In-memory collections</span></span> |
| [<span data-ttu-id="72d07-287">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="72d07-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="72d07-288">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="72d07-288">File in the user profile directory</span></span> |

<span data-ttu-id="72d07-289">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="72d07-290">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="72d07-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="72d07-291">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="72d07-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="72d07-292">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="72d07-292">*appsettings.json*</span></span>
1. <span data-ttu-id="72d07-293">*AppSettings*. `Environment`. *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="72d07-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="72d07-294">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="72d07-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="72d07-295">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="72d07-296">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="72d07-297">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="72d07-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="72d07-298">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="72d07-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="72d07-299">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="72d07-299">Connection string prefixes</span></span>

<span data-ttu-id="72d07-300">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="72d07-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="72d07-301">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="72d07-302">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="72d07-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="72d07-303">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="72d07-303">Connection string prefix</span></span> | <span data-ttu-id="72d07-304">Dostawca</span><span class="sxs-lookup"><span data-stu-id="72d07-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="72d07-305">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="72d07-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="72d07-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="72d07-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="72d07-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="72d07-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="72d07-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="72d07-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="72d07-309">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="72d07-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="72d07-310">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="72d07-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="72d07-311">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem `CUSTOMCONNSTR_`tego, który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="72d07-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="72d07-312">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="72d07-312">Environment variable key</span></span> | <span data-ttu-id="72d07-313">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-313">Converted configuration key</span></span> | <span data-ttu-id="72d07-314">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="72d07-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-315">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="72d07-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-316">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="72d07-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d07-317">Wartość:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d07-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-318">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="72d07-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d07-319">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d07-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-320">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="72d07-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d07-321">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d07-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="72d07-322">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="72d07-322">JSON configuration provider</span></span>

<span data-ttu-id="72d07-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="72d07-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="72d07-324">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="72d07-324">Overloads can specify:</span></span>

* <span data-ttu-id="72d07-325">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="72d07-325">Whether the file is optional.</span></span>
* <span data-ttu-id="72d07-326">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="72d07-327">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="72d07-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="72d07-328">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-328">The preceding code:</span></span>

* <span data-ttu-id="72d07-329">Konfiguruje dostawcę konfiguracji JSON w celu załadowania pliku *. JSON* z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="72d07-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="72d07-330">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="72d07-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="72d07-331">`reloadOnChange: true`: Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="72d07-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="72d07-332">Odczytuje [domyślnych dostawców konfiguracji](#default) przed plikiem moja *config. JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d07-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="72d07-333">Ustawienia w pliku *config. JSON* przesłaniają ustawienie w domyślnych dostawcach konfiguracji, w tym [dostawcę konfiguracji zmienne środowiskowe](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d07-334">Zwykle ***nie*** chcesz, aby niestandardowy plik JSON zastępujący wartości ustawione w [zmiennej środowiskowej dostawcy konfiguracji](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d07-335">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="72d07-336">W powyższym kodzie ustawienia w *pliku config. JSON* i *konfiguracji*. `Environment`. pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="72d07-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="72d07-337">Zastąp ustawienia w pliku *appSettings. JSON* i *AppSettings*. `Environment`. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d07-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="72d07-338">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d07-339">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *. JSON* :</span><span class="sxs-lookup"><span data-stu-id="72d07-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="72d07-340">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="72d07-341">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="72d07-341">File configuration provider</span></span>

<span data-ttu-id="72d07-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="72d07-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="72d07-343">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="72d07-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="72d07-344">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="72d07-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="72d07-345">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="72d07-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="72d07-346">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="72d07-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="72d07-347">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="72d07-347">INI configuration provider</span></span>

<span data-ttu-id="72d07-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="72d07-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d07-349">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="72d07-350">W powyższym kodzie ustawienia w *MyIniConfig. ini* i *MyIniConfig*. `Environment`. pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="72d07-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="72d07-351">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="72d07-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="72d07-352">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d07-353">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="72d07-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="72d07-354">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="72d07-355">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="72d07-355">XML configuration provider</span></span>

<span data-ttu-id="72d07-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="72d07-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d07-357">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="72d07-358">W powyższym kodzie ustawienia w *MyXMLFile. XML* i *MyXMLFile*. `Environment`. pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="72d07-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="72d07-359">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="72d07-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="72d07-360">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d07-361">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="72d07-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="72d07-362">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-363">Powtarzające się elementy, które używają tej samej nazwy elementu `name` , działają, jeśli atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="72d07-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="72d07-364">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="72d07-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-365">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="72d07-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="72d07-366">Poprzedni plik konfiguracji ładuje następujące klucze z `value`:</span><span class="sxs-lookup"><span data-stu-id="72d07-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d07-367">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="72d07-367">key:attribute</span></span>
* <span data-ttu-id="72d07-368">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="72d07-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="72d07-369">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="72d07-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="72d07-370"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="72d07-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="72d07-371">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-371">The key is the file name.</span></span> <span data-ttu-id="72d07-372">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-372">The value contains the file's contents.</span></span> <span data-ttu-id="72d07-373">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="72d07-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="72d07-374">Aby uaktywnić konfigurację klucza dla plików, wywołaj metodę <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72d07-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="72d07-375">`directoryPath` Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="72d07-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="72d07-376">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d07-377">`Action<KeyPerFileConfigurationSource>` Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="72d07-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="72d07-378">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="72d07-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="72d07-379">Podwójny znak podkreślenia (`__`) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="72d07-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="72d07-380">Na przykład nazwa `Logging__LogLevel__System` pliku generuje klucz `Logging:LogLevel:System`konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="72d07-381">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="72d07-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="72d07-382">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="72d07-382">Memory configuration provider</span></span>

<span data-ttu-id="72d07-383"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="72d07-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="72d07-384">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="72d07-385">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-386">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="72d07-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="72d07-387">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="72d07-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="72d07-388">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="72d07-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="72d07-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="72d07-389">GetValue</span></span>

<span data-ttu-id="72d07-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="72d07-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-391">W powyższym kodzie, `NumberKey` Jeśli nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="72d07-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="72d07-392">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="72d07-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="72d07-393">W poniższych przykładach należy wziąć pod uwagę następujący plik *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="72d07-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="72d07-394">Poniższy kod dodaje *MySubsection. JSON* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="72d07-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="72d07-395">GetSection</span></span>

<span data-ttu-id="72d07-396">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="72d07-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="72d07-397">Poniższy kod zwraca wartości dla `section1`:</span><span class="sxs-lookup"><span data-stu-id="72d07-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-398">Poniższy kod zwraca wartości dla `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="72d07-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-399">`GetSection`nigdy nie `null`zwraca.</span><span class="sxs-lookup"><span data-stu-id="72d07-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="72d07-400">Jeśli nie znaleziono pasującej sekcji, zwracany jest `IConfigurationSection` pusty.</span><span class="sxs-lookup"><span data-stu-id="72d07-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="72d07-401">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="72d07-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="72d07-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="72d07-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="72d07-403">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="72d07-403">GetChildren and Exists</span></span>

<span data-ttu-id="72d07-404">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="72d07-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-405">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="72d07-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="72d07-406">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="72d07-406">Bind an array</span></span>

<span data-ttu-id="72d07-407">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d07-408">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="72d07-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="72d07-409">Weź pod uwagę plik *webarray. JSON* z [przykładowego pobrania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="72d07-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="72d07-410">Poniższy kod dodaje plik *webarray. JSON* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="72d07-411">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="72d07-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-412">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="72d07-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="72d07-413">W poprzednich danych wyjściowych indeks 3 ma wartość `value40`odpowiadającą `"4": "value40",` w pliku *webarray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="72d07-414">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="72d07-415">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="72d07-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="72d07-416">Poniższy kod ładuje `array:entries` konfigurację z użyciem metody <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="72d07-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="72d07-417">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="72d07-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-418">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="72d07-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="72d07-419">Indeks &num;3 w obiekcie powiązanym przechowuje dane konfiguracji dla klucza `array:4` konfiguracji i jego wartość `value4`.</span><span class="sxs-lookup"><span data-stu-id="72d07-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="72d07-420">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="72d07-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="72d07-421">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="72d07-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="72d07-422">Brakujący element konfiguracji dla indeksu &num;3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje parę &num;klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="72d07-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="72d07-423">Rozważmy następujący plik *Wartość3. JSON* z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="72d07-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="72d07-424">Poniższy kod zawiera konfigurację dla *Wartość3. JSON* i `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="72d07-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="72d07-425">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="72d07-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="72d07-426">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="72d07-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="72d07-427">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="72d07-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="72d07-428">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-428">Custom configuration provider</span></span>

<span data-ttu-id="72d07-429">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="72d07-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="72d07-430">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="72d07-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="72d07-431">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="72d07-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="72d07-432">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową `ConfigurationBuilder` wartość w celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="72d07-433">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="72d07-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="72d07-434">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="72d07-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="72d07-435">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="72d07-436">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="72d07-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="72d07-437">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="72d07-438">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="72d07-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="72d07-439">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="72d07-440">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="72d07-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="72d07-441">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="72d07-442">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="72d07-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="72d07-443">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="72d07-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="72d07-444">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="72d07-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="72d07-445">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="72d07-446">Metoda `AddEFConfiguration` rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72d07-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="72d07-447">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="72d07-448">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="72d07-449">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="72d07-449">Access configuration in Startup</span></span>

<span data-ttu-id="72d07-450">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="72d07-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="72d07-451">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="72d07-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="72d07-452">Konfiguracja dostępu w Razor Pages</span><span class="sxs-lookup"><span data-stu-id="72d07-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="72d07-453">Poniższy kod przedstawia dane konfiguracji na stronie Razor:</span><span class="sxs-lookup"><span data-stu-id="72d07-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="72d07-454">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="72d07-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="72d07-455">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="72d07-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="72d07-456">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="72d07-456">Host versus app configuration</span></span>

<span data-ttu-id="72d07-457">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="72d07-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="72d07-458">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="72d07-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="72d07-459">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="72d07-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="72d07-460">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="72d07-461">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na <xref:fundamentals/index#host>konfigurację hosta, zobacz.</span><span class="sxs-lookup"><span data-stu-id="72d07-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="72d07-462">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="72d07-462">Default host configuration</span></span>

<span data-ttu-id="72d07-463">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="72d07-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="72d07-464">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="72d07-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="72d07-465">Zmienne środowiskowe poprzedzone `DOTNET_` znakiem (na `DOTNET_ENVIRONMENT`przykład) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="72d07-466">Prefiks (`DOTNET_`) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="72d07-467">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="72d07-468">Konfiguracja domyślna hosta sieci Web (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="72d07-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="72d07-469">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="72d07-470">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="72d07-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="72d07-471">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, `ASPNETCORE_FORWARDEDHEADERS_ENABLED` Jeśli zmienna środowiskowa jest ustawiona na. `true`</span><span class="sxs-lookup"><span data-stu-id="72d07-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="72d07-472">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="72d07-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="72d07-473">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="72d07-473">Other configuration</span></span>

<span data-ttu-id="72d07-474">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="72d07-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="72d07-475">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="72d07-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="72d07-476">*plik Launch. JSON*/*profilu launchsettings. JSON* to pliki konfiguracji narzędzi dla środowiska programistycznego, opisane w temacie:</span><span class="sxs-lookup"><span data-stu-id="72d07-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="72d07-477">W <xref:fundamentals/environments#development>programie.</span><span class="sxs-lookup"><span data-stu-id="72d07-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="72d07-478">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="72d07-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="72d07-479">*Web. config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="72d07-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="72d07-480">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu <xref:migration/proper-to-2x/index#store-configurations>ASP.NET, zobacz.</span><span class="sxs-lookup"><span data-stu-id="72d07-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="72d07-481">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="72d07-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="72d07-482"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="72d07-483">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d07-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72d07-484">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="72d07-484">Additional resources</span></span>

* [<span data-ttu-id="72d07-485">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="72d07-486">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji*.</span><span class="sxs-lookup"><span data-stu-id="72d07-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="72d07-487">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="72d07-488">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d07-488">Azure Key Vault</span></span>
* <span data-ttu-id="72d07-489">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="72d07-489">Azure App Configuration</span></span>
* <span data-ttu-id="72d07-490">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-490">Command-line arguments</span></span>
* <span data-ttu-id="72d07-491">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="72d07-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="72d07-492">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="72d07-492">Directory files</span></span>
* <span data-ttu-id="72d07-493">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="72d07-493">Environment variables</span></span>
* <span data-ttu-id="72d07-494">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="72d07-494">In-memory .NET objects</span></span>
* <span data-ttu-id="72d07-495">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="72d07-495">Settings files</span></span>

<span data-ttu-id="72d07-496">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="72d07-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="72d07-497">Przykłady kodu, które obserwują i w przykładowej aplikacji <xref:Microsoft.Extensions.Configuration> używają przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="72d07-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="72d07-498">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="72d07-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="72d07-499">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="72d07-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="72d07-500">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="72d07-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="72d07-501">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72d07-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="72d07-502">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="72d07-502">Host versus app configuration</span></span>

<span data-ttu-id="72d07-503">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="72d07-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="72d07-504">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="72d07-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="72d07-505">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="72d07-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="72d07-506">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="72d07-507">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na <xref:fundamentals/index#host>konfigurację hosta, zobacz.</span><span class="sxs-lookup"><span data-stu-id="72d07-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="72d07-508">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="72d07-508">Other configuration</span></span>

<span data-ttu-id="72d07-509">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="72d07-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="72d07-510">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="72d07-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="72d07-511">*plik Launch. JSON*/*profilu launchsettings. JSON* to pliki konfiguracji narzędzi dla środowiska programistycznego, opisane w temacie:</span><span class="sxs-lookup"><span data-stu-id="72d07-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="72d07-512">W <xref:fundamentals/environments#development>programie.</span><span class="sxs-lookup"><span data-stu-id="72d07-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="72d07-513">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="72d07-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="72d07-514">*Web. config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="72d07-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="72d07-515">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu <xref:migration/proper-to-2x/index#store-configurations>ASP.NET, zobacz.</span><span class="sxs-lookup"><span data-stu-id="72d07-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="72d07-516">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="72d07-516">Default configuration</span></span>

<span data-ttu-id="72d07-517">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych](/dotnet/core/tools/dotnet-new) szablonów dotnet <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> są wywoływane podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="72d07-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="72d07-518">`CreateDefaultBuilder`zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="72d07-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="72d07-519">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="72d07-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="72d07-520">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="72d07-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="72d07-521">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="72d07-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="72d07-522">Zmienne środowiskowe poprzedzone `ASPNETCORE_` znakiem (na `ASPNETCORE_ENVIRONMENT`przykład) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="72d07-523">Prefiks (`ASPNETCORE_`) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="72d07-524">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="72d07-525">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="72d07-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="72d07-526">*appSettings. JSON* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="72d07-527">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="72d07-528">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana `Development` w środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="72d07-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="72d07-529">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="72d07-530">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="72d07-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="72d07-531">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="72d07-531">Security</span></span>

<span data-ttu-id="72d07-532">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="72d07-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="72d07-533">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="72d07-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="72d07-534">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="72d07-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="72d07-535">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="72d07-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="72d07-536">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="72d07-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="72d07-537"><xref:security/app-secrets>&ndash; Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="72d07-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="72d07-538">Menedżer wpisów tajnych używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="72d07-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="72d07-539">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="72d07-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="72d07-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="72d07-541">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d07-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="72d07-542">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-542">Hierarchical configuration data</span></span>

<span data-ttu-id="72d07-543">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="72d07-544">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="72d07-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="72d07-545">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="72d07-546">Sekcje i klucze są spłaszczone przy użyciu dwukropka (`:`), aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="72d07-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="72d07-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-547">section0:key0</span></span>
* <span data-ttu-id="72d07-548">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-548">section0:key1</span></span>
* <span data-ttu-id="72d07-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-549">section1:key0</span></span>
* <span data-ttu-id="72d07-550">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-550">section1:key1</span></span>

<span data-ttu-id="72d07-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>metody <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="72d07-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="72d07-552">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="72d07-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="72d07-553">Konwencje</span><span class="sxs-lookup"><span data-stu-id="72d07-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="72d07-554">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="72d07-554">Sources and providers</span></span>

<span data-ttu-id="72d07-555">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="72d07-556">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="72d07-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="72d07-557">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="72d07-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="72d07-558"><xref:Microsoft.Extensions.Configuration.IConfiguration>jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="72d07-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>można wstrzyknąć do Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC <xref:Microsoft.AspNetCore.Mvc.Controller> , aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="72d07-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="72d07-560">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="72d07-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="72d07-561">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="72d07-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="72d07-562">Klucze</span><span class="sxs-lookup"><span data-stu-id="72d07-562">Keys</span></span>

<span data-ttu-id="72d07-563">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="72d07-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="72d07-564">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="72d07-564">Keys are case-insensitive.</span></span> <span data-ttu-id="72d07-565">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="72d07-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="72d07-566">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="72d07-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="72d07-567">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="72d07-567">Hierarchical keys</span></span>
  * <span data-ttu-id="72d07-568">W interfejsie API konfiguracji, separator dwukropek`:`() działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="72d07-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="72d07-569">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="72d07-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="72d07-570">Podwójne podkreślenie (`__`) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="72d07-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="72d07-571">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="72d07-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="72d07-572">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="72d07-573"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d07-574">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="72d07-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="72d07-575">Wartości</span><span class="sxs-lookup"><span data-stu-id="72d07-575">Values</span></span>

<span data-ttu-id="72d07-576">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="72d07-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="72d07-577">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="72d07-577">Values are strings.</span></span>
* <span data-ttu-id="72d07-578">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="72d07-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="72d07-579">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="72d07-579">Providers</span></span>

<span data-ttu-id="72d07-580">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="72d07-581">Dostawca</span><span class="sxs-lookup"><span data-stu-id="72d07-581">Provider</span></span> | <span data-ttu-id="72d07-582">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="72d07-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="72d07-583">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące*zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="72d07-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="72d07-584">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d07-584">Azure Key Vault</span></span> |
| <span data-ttu-id="72d07-585">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="72d07-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="72d07-586">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="72d07-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="72d07-587">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="72d07-588">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-588">Command-line parameters</span></span> |
| [<span data-ttu-id="72d07-589">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="72d07-590">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="72d07-590">Custom source</span></span> |
| [<span data-ttu-id="72d07-591">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="72d07-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="72d07-592">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="72d07-592">Environment variables</span></span> |
| [<span data-ttu-id="72d07-593">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="72d07-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="72d07-594">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="72d07-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="72d07-595">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="72d07-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="72d07-596">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="72d07-596">Directory files</span></span> |
| [<span data-ttu-id="72d07-597">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="72d07-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="72d07-598">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="72d07-598">In-memory collections</span></span> |
| <span data-ttu-id="72d07-599">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tematy dotyczące*zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="72d07-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="72d07-600">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="72d07-600">File in the user profile directory</span></span> |

<span data-ttu-id="72d07-601">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="72d07-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="72d07-602">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="72d07-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="72d07-603">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="72d07-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="72d07-604">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="72d07-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="72d07-605">Pliki (*appSettings. JSON*, *appSettings. { Environment}. JSON*, gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="72d07-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="72d07-606">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d07-606">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="72d07-607">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="72d07-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="72d07-608">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="72d07-608">Environment variables</span></span>
1. <span data-ttu-id="72d07-609">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-609">Command-line arguments</span></span>

<span data-ttu-id="72d07-610">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="72d07-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="72d07-611">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy `CreateDefaultBuilder`użyciu programu.</span><span class="sxs-lookup"><span data-stu-id="72d07-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d07-612">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="72d07-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="72d07-613">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="72d07-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="72d07-614">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="72d07-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="72d07-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="72d07-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="72d07-616">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="72d07-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="72d07-617">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="72d07-618">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="72d07-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="72d07-619">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="72d07-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="72d07-620">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder`, najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="72d07-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="72d07-621">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="72d07-621">Consume configuration during app startup</span></span>

<span data-ttu-id="72d07-622">Konfiguracja dostarczona do aplikacji w `ConfigureAppConfiguration` programie jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="72d07-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="72d07-623">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="72d07-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="72d07-624">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="72d07-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="72d07-625"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="72d07-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="72d07-626">Aby uaktywnić konfigurację wiersza polecenia, Metoda <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72d07-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d07-627">`AddCommandLine`jest wywoływana automatycznie, `CreateDefaultBuilder(string [])` gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="72d07-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="72d07-628">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="72d07-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d07-629">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="72d07-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="72d07-630">Opcjonalna konfiguracja z pliku *appSettings. JSON* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="72d07-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="72d07-631">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="72d07-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d07-632">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="72d07-632">Environment variables.</span></span>

<span data-ttu-id="72d07-633">`CreateDefaultBuilder`dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="72d07-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="72d07-634">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="72d07-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="72d07-635">`CreateDefaultBuilder`działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="72d07-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="72d07-636">W związku z tym konfiguracja wiersza polecenia aktywowana przez `CreateDefaultBuilder` program może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="72d07-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="72d07-637">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez. `CreateDefaultBuilder`</span><span class="sxs-lookup"><span data-stu-id="72d07-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d07-638">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddCommandLine` jako ostatni.</span><span class="sxs-lookup"><span data-stu-id="72d07-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="72d07-639">**Przyklad**</span><span class="sxs-lookup"><span data-stu-id="72d07-639">**Example**</span></span>

<span data-ttu-id="72d07-640">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="72d07-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="72d07-641">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="72d07-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="72d07-642">Podaj do `dotnet run` polecenia argument wiersza polecenia, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="72d07-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="72d07-643">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="72d07-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="72d07-644">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="72d07-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="72d07-645">Argumenty</span><span class="sxs-lookup"><span data-stu-id="72d07-645">Arguments</span></span>

<span data-ttu-id="72d07-646">Wartość musi następować po znaku równości (`=`) lub klucz musi mieć prefiks (`--` lub `/`), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="72d07-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="72d07-647">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="72d07-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="72d07-648">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="72d07-648">Key prefix</span></span>               | <span data-ttu-id="72d07-649">Przykład</span><span class="sxs-lookup"><span data-stu-id="72d07-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="72d07-650">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="72d07-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="72d07-651">Dwie kreski (`--`)</span><span class="sxs-lookup"><span data-stu-id="72d07-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="72d07-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="72d07-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="72d07-653">Ukośnik (`/`)</span><span class="sxs-lookup"><span data-stu-id="72d07-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="72d07-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="72d07-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="72d07-655">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="72d07-656">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="72d07-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="72d07-657">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="72d07-657">Switch mappings</span></span>

<span data-ttu-id="72d07-658">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="72d07-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="72d07-659">Podczas ręcznego kompilowania konfiguracji za <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>pomocą programu należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="72d07-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="72d07-660">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="72d07-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="72d07-661">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="72d07-662">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską (`-`).</span><span class="sxs-lookup"><span data-stu-id="72d07-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="72d07-663">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="72d07-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="72d07-664">Przełączniki muszą zaczynać się kreską`-`() lub podwójną kreską (`--`).</span><span class="sxs-lookup"><span data-stu-id="72d07-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="72d07-665">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="72d07-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="72d07-666">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="72d07-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="72d07-667">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="72d07-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="72d07-668">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="72d07-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="72d07-669">W przypadku aplikacji korzystających z mapowań przełączników wywołanie `CreateDefaultBuilder` nie powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="72d07-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="72d07-670">Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="72d07-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d07-671">Rozwiązanie nie przekazuje argumentów do `CreateDefaultBuilder` , ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="72d07-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="72d07-672">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="72d07-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="72d07-673">Key</span><span class="sxs-lookup"><span data-stu-id="72d07-673">Key</span></span>       | <span data-ttu-id="72d07-674">Wartość</span><span class="sxs-lookup"><span data-stu-id="72d07-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="72d07-675">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="72d07-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="72d07-676">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="72d07-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="72d07-677">Key</span><span class="sxs-lookup"><span data-stu-id="72d07-677">Key</span></span>               | <span data-ttu-id="72d07-678">Wartość</span><span class="sxs-lookup"><span data-stu-id="72d07-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="72d07-679">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="72d07-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="72d07-680"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="72d07-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="72d07-681">Aby uaktywnić konfigurację zmiennych środowiskowych, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> Wywołaj metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72d07-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="72d07-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="72d07-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="72d07-683">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="72d07-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="72d07-684">`AddEnvironmentVariables`służy do ładowania zmiennych środowiskowych, które są `ASPNETCORE_` poprzedzone [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="72d07-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="72d07-685">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="72d07-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d07-686">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="72d07-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="72d07-687">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez `AddEnvironmentVariables` wywołanie bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="72d07-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="72d07-688">Opcjonalna konfiguracja z pliku *appSettings. JSON* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="72d07-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="72d07-689">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="72d07-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d07-690">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="72d07-690">Command-line arguments.</span></span>

<span data-ttu-id="72d07-691">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="72d07-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="72d07-692">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="72d07-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="72d07-693">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji `ConfigureAppConfiguration` w i `AddEnvironmentVariables` Wywołaj z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="72d07-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="72d07-694">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="72d07-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="72d07-695">**Przyklad**</span><span class="sxs-lookup"><span data-stu-id="72d07-695">**Example**</span></span>

<span data-ttu-id="72d07-696">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="72d07-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="72d07-697">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="72d07-697">Run the sample app.</span></span> <span data-ttu-id="72d07-698">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="72d07-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="72d07-699">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej `ENVIRONMENT`środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="72d07-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="72d07-700">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj `Development` w przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="72d07-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="72d07-701">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="72d07-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="72d07-702">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="72d07-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="72d07-703">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić stronę `FilteredConfiguration` na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="72d07-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="72d07-704">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="72d07-704">Prefixes</span></span>

<span data-ttu-id="72d07-705">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="72d07-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="72d07-706">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_`, podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="72d07-707">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="72d07-708">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="72d07-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="72d07-709">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="72d07-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d07-710">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="72d07-710">**Connection string prefixes**</span></span>

<span data-ttu-id="72d07-711">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="72d07-712">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="72d07-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="72d07-713">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="72d07-713">Connection string prefix</span></span> | <span data-ttu-id="72d07-714">Dostawca</span><span class="sxs-lookup"><span data-stu-id="72d07-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="72d07-715">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="72d07-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="72d07-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="72d07-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="72d07-717">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="72d07-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="72d07-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="72d07-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="72d07-719">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="72d07-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="72d07-720">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="72d07-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="72d07-721">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem `CUSTOMCONNSTR_`tego, który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="72d07-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="72d07-722">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="72d07-722">Environment variable key</span></span> | <span data-ttu-id="72d07-723">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-723">Converted configuration key</span></span> | <span data-ttu-id="72d07-724">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="72d07-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-725">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="72d07-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-726">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="72d07-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d07-727">Wartość:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d07-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-728">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="72d07-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d07-729">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d07-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d07-730">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="72d07-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d07-731">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d07-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="72d07-732">**Przyklad**</span><span class="sxs-lookup"><span data-stu-id="72d07-732">**Example**</span></span>

<span data-ttu-id="72d07-733">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="72d07-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="72d07-734">Nazwa &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="72d07-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="72d07-735">Wartość &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="72d07-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="72d07-736">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config`, odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="72d07-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="72d07-737">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="72d07-737">File Configuration Provider</span></span>

<span data-ttu-id="72d07-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="72d07-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="72d07-739">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="72d07-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="72d07-740">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="72d07-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="72d07-741">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="72d07-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="72d07-742">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="72d07-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="72d07-743">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="72d07-743">INI Configuration Provider</span></span>

<span data-ttu-id="72d07-744"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="72d07-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d07-745">Aby uaktywnić konfigurację pliku INI, wywołaj metodę <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> rozszerzenia w wystąpieniu. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="72d07-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d07-746">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="72d07-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="72d07-747">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d07-748">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="72d07-748">Whether the file is optional.</span></span>
* <span data-ttu-id="72d07-749">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="72d07-750"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="72d07-751">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="72d07-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="72d07-752">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="72d07-752">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="72d07-753">Poprzedni plik konfiguracji ładuje następujące klucze z `value`:</span><span class="sxs-lookup"><span data-stu-id="72d07-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d07-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-754">section0:key0</span></span>
* <span data-ttu-id="72d07-755">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-755">section0:key1</span></span>
* <span data-ttu-id="72d07-756">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="72d07-756">section1:subsection:key</span></span>
* <span data-ttu-id="72d07-757">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="72d07-757">section2:subsection0:key</span></span>
* <span data-ttu-id="72d07-758">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="72d07-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="72d07-759">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="72d07-759">JSON Configuration Provider</span></span>

<span data-ttu-id="72d07-760"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="72d07-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="72d07-761">Aby uaktywnić konfigurację pliku JSON, wywołaj metodę <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> rozszerzenia w wystąpieniu. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="72d07-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d07-762">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d07-763">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="72d07-763">Whether the file is optional.</span></span>
* <span data-ttu-id="72d07-764">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="72d07-765"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="72d07-766">`AddJsonFile`jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy `CreateDefaultBuilder`użyciu.</span><span class="sxs-lookup"><span data-stu-id="72d07-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d07-767">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="72d07-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="72d07-768">*appSettings. JSON* &ndash; ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="72d07-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="72d07-769">Wersja środowiska pliku może przesłonić wartości dostarczone przez plik *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d07-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="72d07-770">*appSettings. {Environment}. JSON* &ndash; wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="72d07-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="72d07-771">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="72d07-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d07-772">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="72d07-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="72d07-773">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="72d07-773">Environment variables.</span></span>
* <span data-ttu-id="72d07-774">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="72d07-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d07-775">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="72d07-775">Command-line arguments.</span></span>

<span data-ttu-id="72d07-776">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="72d07-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="72d07-777">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="72d07-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="72d07-778">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *appSettings. JSON* i *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72d07-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="72d07-779">**Przyklad**</span><span class="sxs-lookup"><span data-stu-id="72d07-779">**Example**</span></span>

<span data-ttu-id="72d07-780">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="72d07-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="72d07-781">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72d07-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="72d07-782">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="72d07-783">Dla *appSettings. Plik Development. JSON* w przykładowej aplikacji jest ładowany:</span><span class="sxs-lookup"><span data-stu-id="72d07-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="72d07-784">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="72d07-784">Run the sample app.</span></span> <span data-ttu-id="72d07-785">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="72d07-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="72d07-786">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="72d07-787">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="72d07-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="72d07-788">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="72d07-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="72d07-789">Otwórz plik *Properties/profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d07-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="72d07-790">W `ConfigurationSample` profilu Zmień wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej na. `Production`</span><span class="sxs-lookup"><span data-stu-id="72d07-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="72d07-791">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="72d07-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="72d07-792">Ustawienia w *appSettings. Plik Development. JSON* nie zastępuje już ustawień w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d07-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="72d07-793">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning`.</span><span class="sxs-lookup"><span data-stu-id="72d07-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="72d07-794">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="72d07-794">XML Configuration Provider</span></span>

<span data-ttu-id="72d07-795"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="72d07-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d07-796">Aby uaktywnić konfigurację pliku XML, wywołaj metodę <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> rozszerzenia w wystąpieniu. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="72d07-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d07-797">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d07-798">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="72d07-798">Whether the file is optional.</span></span>
* <span data-ttu-id="72d07-799">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="72d07-800"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="72d07-801">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="72d07-802">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="72d07-803">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="72d07-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="72d07-804">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="72d07-804">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="72d07-805">Poprzedni plik konfiguracji ładuje następujące klucze z `value`:</span><span class="sxs-lookup"><span data-stu-id="72d07-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d07-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-806">section0:key0</span></span>
* <span data-ttu-id="72d07-807">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-807">section0:key1</span></span>
* <span data-ttu-id="72d07-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-808">section1:key0</span></span>
* <span data-ttu-id="72d07-809">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-809">section1:key1</span></span>

<span data-ttu-id="72d07-810">Powtarzające się elementy, które używają tej samej nazwy elementu `name` , działają, jeśli atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="72d07-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="72d07-811">Poprzedni plik konfiguracji ładuje następujące klucze z `value`:</span><span class="sxs-lookup"><span data-stu-id="72d07-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d07-812">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="72d07-812">section:section0:key:key0</span></span>
* <span data-ttu-id="72d07-813">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-813">section:section0:key:key1</span></span>
* <span data-ttu-id="72d07-814">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="72d07-814">section:section1:key:key0</span></span>
* <span data-ttu-id="72d07-815">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-815">section:section1:key:key1</span></span>

<span data-ttu-id="72d07-816">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="72d07-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="72d07-817">Poprzedni plik konfiguracji ładuje następujące klucze z `value`:</span><span class="sxs-lookup"><span data-stu-id="72d07-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d07-818">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="72d07-818">key:attribute</span></span>
* <span data-ttu-id="72d07-819">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="72d07-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="72d07-820">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="72d07-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="72d07-821"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="72d07-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="72d07-822">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-822">The key is the file name.</span></span> <span data-ttu-id="72d07-823">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="72d07-823">The value contains the file's contents.</span></span> <span data-ttu-id="72d07-824">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="72d07-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="72d07-825">Aby uaktywnić konfigurację klucza dla plików, wywołaj metodę <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72d07-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="72d07-826">`directoryPath` Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="72d07-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="72d07-827">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="72d07-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d07-828">`Action<KeyPerFileConfigurationSource>` Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="72d07-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="72d07-829">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="72d07-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="72d07-830">Podwójny znak podkreślenia (`__`) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="72d07-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="72d07-831">Na przykład nazwa `Logging__LogLevel__System` pliku generuje klucz `Logging:LogLevel:System`konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="72d07-832">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="72d07-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="72d07-833">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="72d07-833">Memory Configuration Provider</span></span>

<span data-ttu-id="72d07-834"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="72d07-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="72d07-835">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="72d07-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d07-836">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="72d07-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="72d07-837">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="72d07-838">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="72d07-839">Słownik jest używany z wywołaniem `AddInMemoryCollection` do zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="72d07-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="72d07-840">GetValue</span></span>

<span data-ttu-id="72d07-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="72d07-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="72d07-842">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="72d07-842">An overload accepts a default value.</span></span>

<span data-ttu-id="72d07-843">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="72d07-843">The following example:</span></span>

* <span data-ttu-id="72d07-844">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="72d07-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="72d07-845">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="72d07-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="72d07-846">Typ wartości `int`.</span><span class="sxs-lookup"><span data-stu-id="72d07-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="72d07-847">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="72d07-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="72d07-848">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="72d07-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="72d07-849">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="72d07-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="72d07-850">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="72d07-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="72d07-851">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="72d07-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="72d07-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-852">section0:key0</span></span>
* <span data-ttu-id="72d07-853">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-853">section0:key1</span></span>
* <span data-ttu-id="72d07-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-854">section1:key0</span></span>
* <span data-ttu-id="72d07-855">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-855">section1:key1</span></span>
* <span data-ttu-id="72d07-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="72d07-857">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="72d07-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="72d07-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="72d07-859">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="72d07-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="72d07-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="72d07-860">GetSection</span></span>

<span data-ttu-id="72d07-861">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="72d07-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="72d07-862">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1`, wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="72d07-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="72d07-863">`configSection` Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="72d07-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="72d07-864">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0`, wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="72d07-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="72d07-865">`GetSection`nigdy nie `null`zwraca.</span><span class="sxs-lookup"><span data-stu-id="72d07-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="72d07-866">Jeśli nie znaleziono pasującej sekcji, zwracany jest `IConfigurationSection` pusty.</span><span class="sxs-lookup"><span data-stu-id="72d07-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="72d07-867">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="72d07-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="72d07-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="72d07-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="72d07-869">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="72d07-869">GetChildren</span></span>

<span data-ttu-id="72d07-870">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w `section2` programie uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="72d07-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="72d07-871">Exists</span><span class="sxs-lookup"><span data-stu-id="72d07-871">Exists</span></span>

<span data-ttu-id="72d07-872">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="72d07-873">Dane przykładowe są `sectionExists` `false` spowodowane tym, że w danych konfiguracji `section2:subsection2` nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="72d07-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="72d07-874">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="72d07-874">Bind to an object graph</span></span>

<span data-ttu-id="72d07-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="72d07-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="72d07-876">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="72d07-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="72d07-877">Przykład zawiera `TvShow` model, którego obiekt zawiera `Metadata` obiekty i `Actors` klasy (*modele/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="72d07-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="72d07-878">Przykładowa aplikacja zawiera plik *tvshow. XML* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="72d07-879">Konfiguracja jest powiązana z wykresem całego `TvShow` obiektu za `Bind` pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="72d07-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="72d07-880">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="72d07-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="72d07-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="72d07-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="72d07-882">`Get<T>`jest wygodniejszy niż używanie `Bind`.</span><span class="sxs-lookup"><span data-stu-id="72d07-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="72d07-883">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="72d07-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="72d07-884">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="72d07-884">Bind an array to a class</span></span>

<span data-ttu-id="72d07-885">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="72d07-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="72d07-886"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d07-887">Każdy format tablicy, który ujawnia segment klucza numerycznego`:0:`( `:1:`, &hellip; `:{n}:`,) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="72d07-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="72d07-888">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="72d07-888">Binding is provided by convention.</span></span> <span data-ttu-id="72d07-889">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="72d07-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="72d07-890">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="72d07-890">**In-memory array processing**</span></span>

<span data-ttu-id="72d07-891">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="72d07-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="72d07-892">Key</span><span class="sxs-lookup"><span data-stu-id="72d07-892">Key</span></span>             | <span data-ttu-id="72d07-893">Wartość</span><span class="sxs-lookup"><span data-stu-id="72d07-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="72d07-894">Tablica: wpisy: 0</span><span class="sxs-lookup"><span data-stu-id="72d07-894">array:entries:0</span></span> | <span data-ttu-id="72d07-895">value0</span><span class="sxs-lookup"><span data-stu-id="72d07-895">value0</span></span> |
| <span data-ttu-id="72d07-896">Tablica: wpisy: 1</span><span class="sxs-lookup"><span data-stu-id="72d07-896">array:entries:1</span></span> | <span data-ttu-id="72d07-897">sekwencj</span><span class="sxs-lookup"><span data-stu-id="72d07-897">value1</span></span> |
| <span data-ttu-id="72d07-898">Tablica: wpisy: 2</span><span class="sxs-lookup"><span data-stu-id="72d07-898">array:entries:2</span></span> | <span data-ttu-id="72d07-899">wartość2</span><span class="sxs-lookup"><span data-stu-id="72d07-899">value2</span></span> |
| <span data-ttu-id="72d07-900">Tablica: wpisy: 4</span><span class="sxs-lookup"><span data-stu-id="72d07-900">array:entries:4</span></span> | <span data-ttu-id="72d07-901">value4</span><span class="sxs-lookup"><span data-stu-id="72d07-901">value4</span></span> |
| <span data-ttu-id="72d07-902">Tablica: wpisy: 5</span><span class="sxs-lookup"><span data-stu-id="72d07-902">array:entries:5</span></span> | <span data-ttu-id="72d07-903">value5</span><span class="sxs-lookup"><span data-stu-id="72d07-903">value5</span></span> |

<span data-ttu-id="72d07-904">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="72d07-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="72d07-905">Tablica pomija wartość dla indeksu &num;3.</span><span class="sxs-lookup"><span data-stu-id="72d07-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="72d07-906">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="72d07-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="72d07-907">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="72d07-908">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="72d07-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="72d07-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="72d07-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="72d07-910">Obiekt powiązany, wystąpienie elementu `ArrayExample`, otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="72d07-911">`ArrayExample.Entries`Indeks</span><span class="sxs-lookup"><span data-stu-id="72d07-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="72d07-912">`ArrayExample.Entries`Wartościami</span><span class="sxs-lookup"><span data-stu-id="72d07-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="72d07-913">0</span><span class="sxs-lookup"><span data-stu-id="72d07-913">0</span></span>                            | <span data-ttu-id="72d07-914">value0</span><span class="sxs-lookup"><span data-stu-id="72d07-914">value0</span></span>                       |
| <span data-ttu-id="72d07-915">1</span><span class="sxs-lookup"><span data-stu-id="72d07-915">1</span></span>                            | <span data-ttu-id="72d07-916">sekwencj</span><span class="sxs-lookup"><span data-stu-id="72d07-916">value1</span></span>                       |
| <span data-ttu-id="72d07-917">2</span><span class="sxs-lookup"><span data-stu-id="72d07-917">2</span></span>                            | <span data-ttu-id="72d07-918">wartość2</span><span class="sxs-lookup"><span data-stu-id="72d07-918">value2</span></span>                       |
| <span data-ttu-id="72d07-919">3</span><span class="sxs-lookup"><span data-stu-id="72d07-919">3</span></span>                            | <span data-ttu-id="72d07-920">value4</span><span class="sxs-lookup"><span data-stu-id="72d07-920">value4</span></span>                       |
| <span data-ttu-id="72d07-921">4</span><span class="sxs-lookup"><span data-stu-id="72d07-921">4</span></span>                            | <span data-ttu-id="72d07-922">value5</span><span class="sxs-lookup"><span data-stu-id="72d07-922">value5</span></span>                       |

<span data-ttu-id="72d07-923">Indeks &num;3 w obiekcie powiązanym przechowuje dane konfiguracji dla klucza `array:4` konfiguracji i jego wartość `value4`.</span><span class="sxs-lookup"><span data-stu-id="72d07-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="72d07-924">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="72d07-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="72d07-925">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="72d07-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="72d07-926">Brakujący element konfiguracji dla indeksu &num;3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="72d07-927">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="72d07-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="72d07-928">plik *missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72d07-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="72d07-929">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="72d07-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="72d07-930">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="72d07-931">Key</span><span class="sxs-lookup"><span data-stu-id="72d07-931">Key</span></span>             | <span data-ttu-id="72d07-932">Wartość</span><span class="sxs-lookup"><span data-stu-id="72d07-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="72d07-933">Tablica: wpisy: 3</span><span class="sxs-lookup"><span data-stu-id="72d07-933">array:entries:3</span></span> | <span data-ttu-id="72d07-934">Wartość3</span><span class="sxs-lookup"><span data-stu-id="72d07-934">value3</span></span> |

<span data-ttu-id="72d07-935">Jeśli wystąpienie `ArrayExample` klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num;3, `ArrayExample.Entries` tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="72d07-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="72d07-936">`ArrayExample.Entries`Indeks</span><span class="sxs-lookup"><span data-stu-id="72d07-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="72d07-937">`ArrayExample.Entries`Wartościami</span><span class="sxs-lookup"><span data-stu-id="72d07-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="72d07-938">0</span><span class="sxs-lookup"><span data-stu-id="72d07-938">0</span></span>                            | <span data-ttu-id="72d07-939">value0</span><span class="sxs-lookup"><span data-stu-id="72d07-939">value0</span></span>                       |
| <span data-ttu-id="72d07-940">1</span><span class="sxs-lookup"><span data-stu-id="72d07-940">1</span></span>                            | <span data-ttu-id="72d07-941">sekwencj</span><span class="sxs-lookup"><span data-stu-id="72d07-941">value1</span></span>                       |
| <span data-ttu-id="72d07-942">2</span><span class="sxs-lookup"><span data-stu-id="72d07-942">2</span></span>                            | <span data-ttu-id="72d07-943">wartość2</span><span class="sxs-lookup"><span data-stu-id="72d07-943">value2</span></span>                       |
| <span data-ttu-id="72d07-944">3</span><span class="sxs-lookup"><span data-stu-id="72d07-944">3</span></span>                            | <span data-ttu-id="72d07-945">Wartość3</span><span class="sxs-lookup"><span data-stu-id="72d07-945">value3</span></span>                       |
| <span data-ttu-id="72d07-946">4</span><span class="sxs-lookup"><span data-stu-id="72d07-946">4</span></span>                            | <span data-ttu-id="72d07-947">value4</span><span class="sxs-lookup"><span data-stu-id="72d07-947">value4</span></span>                       |
| <span data-ttu-id="72d07-948">5</span><span class="sxs-lookup"><span data-stu-id="72d07-948">5</span></span>                            | <span data-ttu-id="72d07-949">value5</span><span class="sxs-lookup"><span data-stu-id="72d07-949">value5</span></span>                       |

<span data-ttu-id="72d07-950">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="72d07-950">**JSON array processing**</span></span>

<span data-ttu-id="72d07-951">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="72d07-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="72d07-952">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="72d07-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="72d07-953">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="72d07-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="72d07-954">Key</span><span class="sxs-lookup"><span data-stu-id="72d07-954">Key</span></span>                     | <span data-ttu-id="72d07-955">Wartość</span><span class="sxs-lookup"><span data-stu-id="72d07-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="72d07-956">json_array: klucz</span><span class="sxs-lookup"><span data-stu-id="72d07-956">json_array:key</span></span>          | <span data-ttu-id="72d07-957">wartośća</span><span class="sxs-lookup"><span data-stu-id="72d07-957">valueA</span></span> |
| <span data-ttu-id="72d07-958">json_array: podsekcja: 0</span><span class="sxs-lookup"><span data-stu-id="72d07-958">json_array:subsection:0</span></span> | <span data-ttu-id="72d07-959">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="72d07-959">valueB</span></span> |
| <span data-ttu-id="72d07-960">json_array: podsekcja: 1</span><span class="sxs-lookup"><span data-stu-id="72d07-960">json_array:subsection:1</span></span> | <span data-ttu-id="72d07-961">valueC</span><span class="sxs-lookup"><span data-stu-id="72d07-961">valueC</span></span> |
| <span data-ttu-id="72d07-962">json_array: podsekcja: 2</span><span class="sxs-lookup"><span data-stu-id="72d07-962">json_array:subsection:2</span></span> | <span data-ttu-id="72d07-963">Znajdując</span><span class="sxs-lookup"><span data-stu-id="72d07-963">valueD</span></span> |

<span data-ttu-id="72d07-964">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="72d07-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="72d07-965">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA`.</span><span class="sxs-lookup"><span data-stu-id="72d07-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="72d07-966">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="72d07-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="72d07-967">`JsonArrayExample.Subsection`Indeks</span><span class="sxs-lookup"><span data-stu-id="72d07-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="72d07-968">`JsonArrayExample.Subsection`Wartościami</span><span class="sxs-lookup"><span data-stu-id="72d07-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="72d07-969">0</span><span class="sxs-lookup"><span data-stu-id="72d07-969">0</span></span>                                   | <span data-ttu-id="72d07-970">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="72d07-970">valueB</span></span>                              |
| <span data-ttu-id="72d07-971">1</span><span class="sxs-lookup"><span data-stu-id="72d07-971">1</span></span>                                   | <span data-ttu-id="72d07-972">valueC</span><span class="sxs-lookup"><span data-stu-id="72d07-972">valueC</span></span>                              |
| <span data-ttu-id="72d07-973">2</span><span class="sxs-lookup"><span data-stu-id="72d07-973">2</span></span>                                   | <span data-ttu-id="72d07-974">Znajdując</span><span class="sxs-lookup"><span data-stu-id="72d07-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="72d07-975">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="72d07-975">Custom configuration provider</span></span>

<span data-ttu-id="72d07-976">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="72d07-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="72d07-977">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="72d07-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="72d07-978">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="72d07-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="72d07-979">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową `ConfigurationBuilder` wartość w celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="72d07-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="72d07-980">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="72d07-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="72d07-981">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="72d07-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="72d07-982">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="72d07-983">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="72d07-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="72d07-984">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="72d07-985">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="72d07-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="72d07-986">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="72d07-987">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="72d07-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="72d07-988">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="72d07-989">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="72d07-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="72d07-990">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="72d07-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="72d07-991">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="72d07-992">Metoda `AddEFConfiguration` rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="72d07-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="72d07-993">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="72d07-994">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="72d07-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="72d07-995">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="72d07-995">Access configuration during startup</span></span>

<span data-ttu-id="72d07-996">Wsuń `IConfiguration` do konstruktora `Startup` , aby uzyskać dostęp do wartości `Startup.ConfigureServices`konfiguracyjnych w.</span><span class="sxs-lookup"><span data-stu-id="72d07-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="72d07-997">Aby uzyskać dostęp do `Startup.Configure`konfiguracji w programie `IConfiguration` , należy wstrzyknąć bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="72d07-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="72d07-998">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="72d07-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="72d07-999">Konfiguracja dostępu na stronie Razor Pages lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="72d07-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="72d07-1000">Aby uzyskać dostęp do ustawień konfiguracji na stronie Razor Pages lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [przestrzeni nazw Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) i wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="72d07-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="72d07-1001">Na stronie Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="72d07-1001">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="72d07-1002">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="72d07-1002">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="72d07-1003">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="72d07-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="72d07-1004"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="72d07-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="72d07-1005">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d07-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72d07-1006">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="72d07-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
