---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core za pomocą interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007161"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="08854-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08854-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="08854-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="08854-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08854-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="08854-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="08854-106">Dostawcy konfiguracji odczytywać dane konfiguracyjne z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="08854-107">Pliki ustawień, takie jak *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="08854-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="08854-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="08854-108">Environment variables</span></span>
* <span data-ttu-id="08854-109">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="08854-109">Azure Key Vault</span></span>
* <span data-ttu-id="08854-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="08854-110">Azure App Configuration</span></span>
* <span data-ttu-id="08854-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-111">Command-line arguments</span></span>
* <span data-ttu-id="08854-112">Dostawcy niestandardowi, zainstalowane lub utworzone</span><span class="sxs-lookup"><span data-stu-id="08854-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="08854-113">Pliki katalogów</span><span class="sxs-lookup"><span data-stu-id="08854-113">Directory files</span></span>
* <span data-ttu-id="08854-114">Obiekty NET w pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-114">In-memory .NET objects</span></span>

<span data-ttu-id="08854-115">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08854-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="08854-116">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="08854-116">Default configuration</span></span>

<span data-ttu-id="08854-117">ASP.NET aplikacje sieci Web Core utworzone za pomocą [dotnet new](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="08854-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="08854-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>zapewnia domyślną konfigurację aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="08854-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="08854-119">[ChainedConfigurationProvider:](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) Dodaje `IConfiguration` istniejące jako źródło.</span><span class="sxs-lookup"><span data-stu-id="08854-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="08854-120">W domyślnej obudowie konfiguracji dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji._</span><span class="sxs-lookup"><span data-stu-id="08854-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="08854-121">[appsettings.json](#appsettingsjson) przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="08854-122">*appsettings.* `Environment` *.json* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="08854-123">Na przykład *appsettings*. ***Produkcja***. *json* i *appsettings*. ***Rozwoju***. *json*.</span><span class="sxs-lookup"><span data-stu-id="08854-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="08854-124">[Wpisy tajne aplikacji,](xref:security/app-secrets) `Development` gdy aplikacja działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="08854-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="08854-125">Zmienne środowiskowe przy użyciu [dostawcy konfiguracji zmienne środowiskowe](#evcp).</span><span class="sxs-lookup"><span data-stu-id="08854-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="08854-126">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="08854-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="08854-127">Dostawcy konfiguracji, którzy są dodani później, zastępują poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="08854-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="08854-128">Na przykład `MyKey` jeśli jest ustawiona w obu *appsettings.json* i środowiska, wartość środowiska jest używana.</span><span class="sxs-lookup"><span data-stu-id="08854-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="08854-129">Korzystając z domyślnych dostawców konfiguracji, [dostawca konfiguracji wiersza polecenia](#command-line-configuration-provider) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="08854-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="08854-130">Aby uzyskać `CreateDefaultBuilder`więcej informacji na temat , zobacz [Domyślne ustawienia konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="08854-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="08854-131">Następujący kod wyświetla włączonych dostawców konfiguracji w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="08854-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="08854-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="08854-132">appsettings.json</span></span>

<span data-ttu-id="08854-133">Należy wziąć pod uwagę następujący plik *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="08854-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="08854-134">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-135">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> konfiguracja ładuje w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="08854-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="08854-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="08854-136">*appsettings.json*</span></span>
1. <span data-ttu-id="08854-137">*appsettings.* `Environment` *.json* : Na przykład *appsettings*. ***Produkcja***. *json* i *appsettings*. ***Rozwoju***. *json* plików.</span><span class="sxs-lookup"><span data-stu-id="08854-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="08854-138">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="08854-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="08854-139">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="08854-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="08854-140">*appsettings*. `Environment`. *json* values override keys in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="08854-141">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="08854-141">For example, by default:</span></span>

* <span data-ttu-id="08854-142">W rozwoju, *appsettings*. ***Rozwoju***. *konfiguracja json* zastępuje wartości znalezione w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="08854-143">W *produkcji, appsettings*. ***Produkcja***. *konfiguracja json* zastępuje wartości znalezione w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="08854-144">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="08854-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="08854-145">Powiązanie hierarchicznych danych konfiguracyjnych przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="08854-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="08854-146">Preferowanym sposobem odczytywania powiązanych wartości konfiguracyjnych jest użycie [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="08854-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="08854-147">Na przykład, aby odczytać następujące wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="08854-148">Utwórz `PositionOptions` następującą klasę:</span><span class="sxs-lookup"><span data-stu-id="08854-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="08854-149">Wszystkie publiczne właściwości odczytu i zapisu typu są powiązane.</span><span class="sxs-lookup"><span data-stu-id="08854-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="08854-150">Pola ***nie*** są powiązane.</span><span class="sxs-lookup"><span data-stu-id="08854-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="08854-151">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="08854-151">The following code:</span></span>

* <span data-ttu-id="08854-152">Wywołuje [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) do `PositionOptions` powiązania `Position` klasy z sekcją.</span><span class="sxs-lookup"><span data-stu-id="08854-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="08854-153">Wyświetla `Position` dane konfiguracyjne.</span><span class="sxs-lookup"><span data-stu-id="08854-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)wiąże i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="08854-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="08854-155">`ConfigurationBinder.Get<T>`może być wygodniejsza `ConfigurationBinder.Bind`niż korzystanie z programu .</span><span class="sxs-lookup"><span data-stu-id="08854-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="08854-156">Poniższy kod pokazuje, `ConfigurationBinder.Get<T>` jak `PositionOptions` używać z klasą:</span><span class="sxs-lookup"><span data-stu-id="08854-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-157">Alternatywnym podejściem podczas korzystania z `Position` ***wzorca opcji*** jest powiązanie sekcji i dodanie jej do [kontenera usługi iniekcji zależności.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="08854-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="08854-158">W poniższym `PositionOptions` kodzie jest dodawany <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> do kontenera usługi z konfiguracją i powiązany z nią:</span><span class="sxs-lookup"><span data-stu-id="08854-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="08854-159">Przy użyciu poprzedniego kodu, następujący kod odczytuje opcje pozycji:</span><span class="sxs-lookup"><span data-stu-id="08854-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-160">Przy użyciu [domyślnej](#default) konfiguracji, *appsettings.json* i *appsettings.* `Environment` *Pliki .json* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="08854-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="08854-161">Zmiany wprowadzone w *appsettings.json* i *appsettings.* `Environment` *.json* ***po*** uruchomieniu aplikacji są odczytywane przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="08854-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="08854-162">Zobacz [dostawcę konfiguracji JSON](#jcp) w tym dokumencie, aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracyjnych JSON.</span><span class="sxs-lookup"><span data-stu-id="08854-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="08854-163">Bezpieczeństwo i tajny menedżer</span><span class="sxs-lookup"><span data-stu-id="08854-163">Security and secret manager</span></span>

<span data-ttu-id="08854-164">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="08854-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="08854-165">Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie dostawcy konfiguracji lub w plikach konfiguracyjnych w postaci zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="08854-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="08854-166">[Menedżer secret](xref:security/app-secrets) może służyć do przechowywania wpisów tajnych w rozwoju.</span><span class="sxs-lookup"><span data-stu-id="08854-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="08854-167">Nie używaj wpisów tajnych produkcji w środowiskach deweloperskich lub testowych.</span><span class="sxs-lookup"><span data-stu-id="08854-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="08854-168">Określ wpisy tajne poza projektem, tak aby nie można przypadkowo zatwierdzone do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="08854-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="08854-169">[Domyślnie](#default) [menedżer secret](xref:security/app-secrets) odczytuje ustawienia konfiguracji po *appsettings.json* i *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="08854-170">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="08854-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="08854-171"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="08854-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="08854-172">Menedżer tajny używa [dostawcy konfiguracji pliku](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="08854-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="08854-173">[Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpiecznie przechowuje wpisy tajne aplikacji dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08854-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="08854-174">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="08854-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="08854-175">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="08854-175">Environment variables</span></span>

<span data-ttu-id="08854-176">Przy użyciu konfiguracji <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> [domyślnej,](#default) konfiguracja ładuje z pary klucza-wartość zmiennej środowiskowej po *przeczytaniu appsettings.json*, *appsettings.* `Environment` *.json*i [Secret manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="08854-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="08854-177">W związku z tym kluczowe wartości odczytywane ze środowiska zastępują wartości odczytywane z *pliku appsettings.json*, *appsettings.* `Environment` *.json*i secret manager.</span><span class="sxs-lookup"><span data-stu-id="08854-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="08854-178">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="08854-178">The following `set` commands:</span></span>

* <span data-ttu-id="08854-179">Ustaw klucze środowiska i wartości [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="08854-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="08854-180">Przetestuj ustawienia podczas korzystania z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="08854-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="08854-181">Polecenie `dotnet run` musi być uruchomione w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="08854-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="08854-182">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="08854-182">The preceding environment settings:</span></span>

* <span data-ttu-id="08854-183">Są ustawiane tylko w procesach uruchomionych z okna poleceń, w które zostały ustawione.</span><span class="sxs-lookup"><span data-stu-id="08854-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="08854-184">Nie będą odczytywane przez przeglądarki uruchomione za pomocą programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="08854-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="08854-185">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy i wartości środowiska w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="08854-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="08854-186">W `set` `setx` przeciwieństwie do , ustawienia są zachowywane.</span><span class="sxs-lookup"><span data-stu-id="08854-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="08854-187">`/M`ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="08854-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="08854-188">Jeśli `/M` przełącznik nie jest używany, ustawiona jest zmienna środowiskowa użytkownika.</span><span class="sxs-lookup"><span data-stu-id="08854-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="08854-189">Aby sprawdzić, czy poprzednie polecenia zastępują *appsettings.json* i *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="08854-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="08854-190">Z visual studio: Exit i restart Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="08854-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="08854-191">Z interfejsu wiersza polecenia: Uruchom `dotnet run`nowe okno polecenia i wprowadź .</span><span class="sxs-lookup"><span data-stu-id="08854-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="08854-192">Wywołanie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="08854-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="08854-193">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="08854-193">In the preceding code:</span></span>

* <span data-ttu-id="08854-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="08854-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="08854-195">Aby uzyskać przykład zamawiania dostawców konfiguracji, zobacz [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="08854-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="08854-196">Zmienne środowiskowe `MyCustomPrefix_` ustawione z prefiksem zastępują [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="08854-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="08854-197">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="08854-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="08854-198">Prefiks jest usuwany po odczytaniu par klucza konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="08854-199">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="08854-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="08854-200">[Domyślna konfiguracja](#default) ładuje zmienne środowiskowe i `DOTNET_` `ASPNETCORE_`argumenty wiersza polecenia poprzedzone i .</span><span class="sxs-lookup"><span data-stu-id="08854-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="08854-201">Prefiksy `DOTNET_` i `ASPNETCORE_` są używane przez ASP.NET Core dla konfiguracji [hosta i aplikacji,](xref:fundamentals/host/generic-host#host-configuration)ale nie dla konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="08854-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="08854-202">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [Host ogólny .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="08854-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="08854-203">W [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję Nowe ustawienie **aplikacji** na stronie Ustawienia **> konfiguracja.**</span><span class="sxs-lookup"><span data-stu-id="08854-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="08854-204">Ustawienia aplikacji usługi Azure App Service to:</span><span class="sxs-lookup"><span data-stu-id="08854-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="08854-205">Szyfrowane w spoczynku i przesyłane za pośrednictwem zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="08854-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="08854-206">Narażone jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="08854-206">Exposed as environment variables.</span></span>

<span data-ttu-id="08854-207">Aby uzyskać więcej informacji, zobacz [Usługi Azure Apps: Zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="08854-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="08854-208">Zobacz [Prefiksy ciągu połączenia,](#constr) aby uzyskać informacje na temat ciągów połączeń bazy danych platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="08854-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="08854-209">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-209">Command-line</span></span>

<span data-ttu-id="08854-210">Przy użyciu konfiguracji <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> [domyślnej](#default) konfiguracja ładuje z argumentu wiersza polecenia pary klucz-wartość po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="08854-211">*appsettings.json* i *appsettings*. `Environment`. *json* plików.</span><span class="sxs-lookup"><span data-stu-id="08854-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="08854-212">[Wpisy tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="08854-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="08854-213">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="08854-213">Environment variables.</span></span>

<span data-ttu-id="08854-214">[Domyślnie](#default)wartości konfiguracyjne ustawione w wierszu polecenia zastępują wartości konfiguracji ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="08854-215">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-215">Command-line arguments</span></span>

<span data-ttu-id="08854-216">Następujące polecenie ustawia klucze `=`i wartości za pomocą:</span><span class="sxs-lookup"><span data-stu-id="08854-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="08854-217">Następujące polecenie ustawia klucze `/`i wartości za pomocą:</span><span class="sxs-lookup"><span data-stu-id="08854-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="08854-218">Następujące polecenie ustawia klucze `--`i wartości za pomocą:</span><span class="sxs-lookup"><span data-stu-id="08854-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="08854-219">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="08854-219">The key value:</span></span>

* <span data-ttu-id="08854-220">Musi `=`następować , lub klucz `--` musi `/` mieć prefiks lub gdy wartość następuje spacji.</span><span class="sxs-lookup"><span data-stu-id="08854-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="08854-221">Nie jest wymagane, `=` jeśli jest używany.</span><span class="sxs-lookup"><span data-stu-id="08854-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="08854-222">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="08854-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="08854-223">W ramach tego samego polecenia nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają `=` par klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="08854-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="08854-224">Mapowania przełączników</span><span class="sxs-lookup"><span data-stu-id="08854-224">Switch mappings</span></span>

<span data-ttu-id="08854-225">Mapowania przełączników umożliwiają logikę zastępowania nazw **kluczy.**</span><span class="sxs-lookup"><span data-stu-id="08854-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="08854-226">Podaj słownik zamienników przełącznika do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="08854-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="08854-227">Gdy używany jest słownik mapowania przełącznika, słownik jest sprawdzany pod kątem klucza zgodnego z kluczem dostarczonym przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="08854-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="08854-228">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika jest przekazywana z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="08854-229">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza`-`polecenia poprzedzonym pojedynczą kreską ( ).</span><span class="sxs-lookup"><span data-stu-id="08854-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="08854-230">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="08854-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="08854-231">Przełączniki muszą `-` zaczynać się od lub `--`.</span><span class="sxs-lookup"><span data-stu-id="08854-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="08854-232">Słownik mapowań przełączników nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="08854-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="08854-233">Aby użyć słownika mapowań przełączników, należy `AddCommandLine`przekazać go do wywołania:</span><span class="sxs-lookup"><span data-stu-id="08854-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="08854-234">Poniższy kod pokazuje wartości kluczy dla zastąpionych kluczy:</span><span class="sxs-lookup"><span data-stu-id="08854-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-235">Uruchom następujące polecenie, aby przetestować wymianę klucza:</span><span class="sxs-lookup"><span data-stu-id="08854-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="08854-236">Uwaga: Obecnie `=` nie można używać do ustawiania wartości `-`zastępowania kluczy za pomocą pojedynczej kreski .</span><span class="sxs-lookup"><span data-stu-id="08854-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="08854-237">Zobacz [ten problem z githubem](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="08854-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="08854-238">Następujące polecenie działa w celu przetestowania wymiany klucza:</span><span class="sxs-lookup"><span data-stu-id="08854-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="08854-239">W przypadku aplikacji korzystających z `CreateDefaultBuilder` mapowań przełączników wywołanie nie powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="08854-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="08854-240">Wywołanie metody nie zawiera mapowanych przełączników i nie ma możliwości przekazania słownika mapowania przełączników do `CreateDefaultBuilder`. `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="08854-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="08854-241">Rozwiązanie nie jest przekazywanie argumentów, `CreateDefaultBuilder` ale zamiast `ConfigurationBuilder` tego, `AddCommandLine` aby umożliwić metody do przetwarzania zarówno argumenty i słownik mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="08854-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="08854-242">Hierarchiczne dane konfiguracyjne</span><span class="sxs-lookup"><span data-stu-id="08854-242">Hierarchical configuration data</span></span>

<span data-ttu-id="08854-243">Interfejs API konfiguracji odczytuje dane konfiguracji hierarchicznej przez spłaszczenie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="08854-244">[Przykładowe pobieranie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="08854-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="08854-245">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-246">Preferowanym sposobem odczytywania danych konfiguracji hierarchicznej jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="08854-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="08854-247">Aby uzyskać więcej informacji, zobacz [Powiązanie danych konfiguracji hierarchicznej](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="08854-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="08854-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>i <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody są dostępne do izolowania sekcji i obrażeń części sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="08854-249">Metody te są opisane później w [GetSection, GetChildren i Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="08854-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="08854-250">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-250">Configuration keys and values</span></span>

<span data-ttu-id="08854-251">Klucze konfiguracyjne:</span><span class="sxs-lookup"><span data-stu-id="08854-251">Configuration keys:</span></span>

* <span data-ttu-id="08854-252">Czy niewrażliwe na decyzje.</span><span class="sxs-lookup"><span data-stu-id="08854-252">Are case-insensitive.</span></span> <span data-ttu-id="08854-253">Na przykład `ConnectionString` `connectionstring` i są traktowane jako klucze równoważne.</span><span class="sxs-lookup"><span data-stu-id="08854-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="08854-254">Jeśli klucz i wartość jest ustawiona w więcej niż jeden dostawca konfiguracji, używana jest wartość z ostatniego dostawcy dodane.</span><span class="sxs-lookup"><span data-stu-id="08854-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="08854-255">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="08854-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="08854-256">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="08854-256">Hierarchical keys</span></span>
  * <span data-ttu-id="08854-257">W interfejsie API konfiguracji separator dwukropka (`:`) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="08854-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="08854-258">W zmiennych środowiskowych separator dwukropek może nie działać na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="08854-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="08854-259">Podwójne podkreślenie, `__`jest obsługiwane przez wszystkie platformy i jest `:`automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="08854-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="08854-260">W usłudze Azure Key `--` Vault klucze hierarchiczne używają jako separatora.</span><span class="sxs-lookup"><span data-stu-id="08854-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="08854-261">[Dostawca konfiguracji usługi Azure Key](xref:security/key-vault-configuration) `--` Vault `:` automatycznie zastępuje się, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="08854-262">Obsługuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> tablice powiązania do obiektów przy użyciu indeksów tablicy w kluczach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="08854-263">Powiązanie tablicy jest opisane w [Bind tablicy do](#boa) sekcji klasy.</span><span class="sxs-lookup"><span data-stu-id="08854-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="08854-264">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-264">Configuration values:</span></span>

* <span data-ttu-id="08854-265">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="08854-265">Are strings.</span></span>
* <span data-ttu-id="08854-266">Wartości null nie mogą być przechowywane w konfiguracji lub powiązane z obiektami.</span><span class="sxs-lookup"><span data-stu-id="08854-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="08854-267">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-267">Configuration providers</span></span>

<span data-ttu-id="08854-268">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET aplikacji Core.</span><span class="sxs-lookup"><span data-stu-id="08854-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="08854-269">Dostawca</span><span class="sxs-lookup"><span data-stu-id="08854-269">Provider</span></span> | <span data-ttu-id="08854-270">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="08854-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="08854-271">Dostawca konfiguracji usługi Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="08854-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="08854-272">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="08854-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="08854-273">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="08854-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="08854-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="08854-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="08854-275">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="08854-276">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-276">Command-line parameters</span></span> |
| [<span data-ttu-id="08854-277">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="08854-278">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="08854-278">Custom source</span></span> |
| [<span data-ttu-id="08854-279">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="08854-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="08854-280">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="08854-280">Environment variables</span></span> |
| [<span data-ttu-id="08854-281">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="08854-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="08854-282">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="08854-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="08854-283">Dostawca konfiguracji klucza na plik</span><span class="sxs-lookup"><span data-stu-id="08854-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="08854-284">Pliki katalogów</span><span class="sxs-lookup"><span data-stu-id="08854-284">Directory files</span></span> |
| [<span data-ttu-id="08854-285">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="08854-286">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-286">In-memory collections</span></span> |
| [<span data-ttu-id="08854-287">Tajny menedżer</span><span class="sxs-lookup"><span data-stu-id="08854-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="08854-288">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="08854-288">File in the user profile directory</span></span> |

<span data-ttu-id="08854-289">Źródła konfiguracji są odczytywane w kolejności, w yszczególnie ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="08854-290">Dostawców konfiguracji zamówienia w kodzie, aby dopasować priorytety dla podstawowych źródeł konfiguracji, które wymaga aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="08854-291">Typowa sekwencja dostawców konfiguracji to:</span><span class="sxs-lookup"><span data-stu-id="08854-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="08854-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="08854-292">*appsettings.json*</span></span>
1. <span data-ttu-id="08854-293">*appsettings*. `Environment`. *json ( json )*</span><span class="sxs-lookup"><span data-stu-id="08854-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="08854-294">Tajny menedżer</span><span class="sxs-lookup"><span data-stu-id="08854-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="08854-295">Zmienne środowiskowe przy użyciu [dostawcy konfiguracji zmienne środowiskowe](#evcp).</span><span class="sxs-lookup"><span data-stu-id="08854-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="08854-296">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="08854-297">Powszechną praktyką jest dodanie dostawcy konfiguracji wiersza polecenia jako ostatniego w serii dostawców, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji ustawionej przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="08854-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="08854-298">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="08854-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="08854-299">Prefiksy ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="08854-299">Connection string prefixes</span></span>

<span data-ttu-id="08854-300">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych ciągu połączenia.</span><span class="sxs-lookup"><span data-stu-id="08854-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="08854-301">Te parametry połączenia są zaangażowane w konfigurowanie ciągów połączeń platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="08854-302">Zmienne środowiskowe z prefiksami pokazanymi w tabeli są ładowane do `AddEnvironmentVariables`aplikacji z [domyślną konfiguracją](#default) lub gdy do pliku .</span><span class="sxs-lookup"><span data-stu-id="08854-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="08854-303">Prefiks ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="08854-303">Connection string prefix</span></span> | <span data-ttu-id="08854-304">Dostawca</span><span class="sxs-lookup"><span data-stu-id="08854-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="08854-305">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="08854-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="08854-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="08854-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="08854-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="08854-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="08854-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="08854-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="08854-309">Po wykryciu i załadowaniu zmiennej środowiskowej do konfiguracji z dowolnym z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="08854-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="08854-310">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej`ConnectionStrings`środowiskowej i dodanie sekcji klucza konfiguracji ( ).</span><span class="sxs-lookup"><span data-stu-id="08854-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="08854-311">Tworzona jest nowa para klucza konfiguracji i wartość `CUSTOMCONNSTR_`reprezentująca dostawcę połączenia bazy danych (z wyjątkiem , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="08854-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="08854-312">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="08854-312">Environment variable key</span></span> | <span data-ttu-id="08854-313">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-313">Converted configuration key</span></span> | <span data-ttu-id="08854-314">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="08854-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-315">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="08854-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-316">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="08854-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="08854-317">Wartość:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="08854-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-318">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="08854-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="08854-319">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="08854-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-320">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="08854-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="08854-321">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="08854-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="08854-322">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="08854-322">JSON configuration provider</span></span>

<span data-ttu-id="08854-323">Konfiguracja <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> ładowania z par klucza klucza json.</span><span class="sxs-lookup"><span data-stu-id="08854-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="08854-324">Przeciążenia można określić:</span><span class="sxs-lookup"><span data-stu-id="08854-324">Overloads can specify:</span></span>

* <span data-ttu-id="08854-325">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="08854-325">Whether the file is optional.</span></span>
* <span data-ttu-id="08854-326">Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.</span><span class="sxs-lookup"><span data-stu-id="08854-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="08854-327">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="08854-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="08854-328">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="08854-328">The preceding code:</span></span>

* <span data-ttu-id="08854-329">Konfiguruje dostawcę konfiguracji JSON, aby załadować plik *MyConfig.json* z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="08854-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="08854-330">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="08854-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="08854-331">`reloadOnChange: true`: Plik zostanie ponownie załadowany po zapisaniu zmian.</span><span class="sxs-lookup"><span data-stu-id="08854-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="08854-332">Odczytuje [domyślnych dostawców konfiguracji](#default) przed plikiem *MyConfig.json.*</span><span class="sxs-lookup"><span data-stu-id="08854-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="08854-333">Ustawienia w ustawieniu zastępowania pliku *MyConfig.json* w domyślnych dostawcach konfiguracji, w tym [dostawcy konfiguracji zmiennych środowiska](#evcp) i dostawcy konfiguracji [wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="08854-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="08854-334">Zazwyczaj ***nie*** chcesz, aby niestandardowe wartości zastępowania pliku JSON były ustawiane w [dostawcy konfiguracji zmiennych środowiska](#evcp) i dostawcy konfiguracji [wiersza polecenia.](#clcp)</span><span class="sxs-lookup"><span data-stu-id="08854-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="08854-335">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="08854-336">W poprzednim kodzie ustawienia w *plikach MyConfig.json* i *MyConfig*. `Environment`. *pliki json:*</span><span class="sxs-lookup"><span data-stu-id="08854-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="08854-337">Zastąpokaj ustawienia w *pliku appsettings.json* i *appsettings*. `Environment`. *json* plików.</span><span class="sxs-lookup"><span data-stu-id="08854-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="08854-338">Są zastępowane przez ustawienia dostawcy [konfiguracji zmiennych środowiska](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="08854-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="08854-339">[Przykładowy plik pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyConfig.json:*</span><span class="sxs-lookup"><span data-stu-id="08854-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="08854-340">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="08854-341">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="08854-341">File configuration provider</span></span>

<span data-ttu-id="08854-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą podstawową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="08854-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="08854-343">Następujący dostawcy konfiguracji wywodzą się z: `FileConfigurationProvider`</span><span class="sxs-lookup"><span data-stu-id="08854-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="08854-344">Dostawca konfiguracji INI</span><span class="sxs-lookup"><span data-stu-id="08854-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="08854-345">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="08854-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="08854-346">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="08854-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="08854-347">Dostawca konfiguracji INI</span><span class="sxs-lookup"><span data-stu-id="08854-347">INI configuration provider</span></span>

<span data-ttu-id="08854-348">Konfiguracja <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> ładowania z par klucza klucza-wartość pliku INI w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="08854-349">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="08854-350">W poprzednim kodzie ustawienia w *myiniconfig.ini* i *MyIniConfig*. `Environment`. pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="08854-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="08854-351">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="08854-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="08854-352">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="08854-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="08854-353">[Przykładowy plik pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini:*</span><span class="sxs-lookup"><span data-stu-id="08854-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="08854-354">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="08854-355">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="08854-355">XML configuration provider</span></span>

<span data-ttu-id="08854-356">Konfiguracja <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> ładowania z par klucza klucza-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="08854-357">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="08854-358">W poprzednim kodzie ustawienia w *plikach MyXMLFile.xml* i *MyXMLFile*. `Environment`. Pliki *xml* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="08854-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="08854-359">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="08854-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="08854-360">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="08854-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="08854-361">[Przykładowy plik download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml:*</span><span class="sxs-lookup"><span data-stu-id="08854-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="08854-362">Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-363">Powtarzające się elementy, które używają `name` tej samej nazwy elementu, działają, jeśli atrybut jest używany do rozróżniania elementów:</span><span class="sxs-lookup"><span data-stu-id="08854-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="08854-364">Poniższy kod odczytuje poprzedni plik konfiguracyjny i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="08854-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-365">Atrybuty mogą być używane do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="08854-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="08854-366">Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`</span><span class="sxs-lookup"><span data-stu-id="08854-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="08854-367">klucz:atrybut</span><span class="sxs-lookup"><span data-stu-id="08854-367">key:attribute</span></span>
* <span data-ttu-id="08854-368">sekcja:klucz:atrybut</span><span class="sxs-lookup"><span data-stu-id="08854-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="08854-369">Dostawca konfiguracji klucza na plik</span><span class="sxs-lookup"><span data-stu-id="08854-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="08854-370">Używa <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> plików katalogu jako par klucza konfiguracji wartości.</span><span class="sxs-lookup"><span data-stu-id="08854-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="08854-371">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-371">The key is the file name.</span></span> <span data-ttu-id="08854-372">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-372">The value contains the file's contents.</span></span> <span data-ttu-id="08854-373">Dostawca konfiguracji klucza na plik jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="08854-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="08854-374">Aby aktywować konfigurację klucza <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> na plik, należy <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>wywołać metodę rozszerzenia w wystąpieniu programu .</span><span class="sxs-lookup"><span data-stu-id="08854-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="08854-375">Do `directoryPath` plików musi być ścieżka bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="08854-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="08854-376">Przeciążenia pozwalają na określenie:</span><span class="sxs-lookup"><span data-stu-id="08854-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="08854-377">Pełnomocnik, `Action<KeyPerFileConfigurationSource>` który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="08854-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="08854-378">Czy katalog jest opcjonalny i ścieżka do katalogu.</span><span class="sxs-lookup"><span data-stu-id="08854-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="08854-379">Podwójny znak podkreślenia (`__`) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="08854-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="08854-380">Na przykład nazwa `Logging__LogLevel__System` pliku tworzy klucz `Logging:LogLevel:System`konfiguracji .</span><span class="sxs-lookup"><span data-stu-id="08854-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="08854-381">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="08854-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="08854-382">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-382">Memory configuration provider</span></span>

<span data-ttu-id="08854-383">Używa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekcji w pamięci jako pary klucz konfiguracji wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="08854-384">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="08854-385">Następujący kod z [pobierania przykładowego](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-386">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="08854-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="08854-387">Aby uzyskać przykład zamawiania dostawców konfiguracji, zobacz [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="08854-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="08854-388">Aby uzyskać przykład zamawiania dostawców konfiguracji, zobacz [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="08854-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="08854-389">Zobacz [Powiąż tablicę](#boa) dla innego przykładu przy użyciu `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="08854-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="08854-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="08854-390">GetValue</span></span>

<span data-ttu-id="08854-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji za pomocą określonego klucza i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="08854-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-392">W poprzednim kodzie, `NumberKey` jeśli nie zostanie znaleziony w konfiguracji, używana jest wartość domyślna. `99`</span><span class="sxs-lookup"><span data-stu-id="08854-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="08854-393">GetSection, GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="08854-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="08854-394">W kolejnych przykładach należy wziąć pod uwagę następujący plik *MySubsection.json:*</span><span class="sxs-lookup"><span data-stu-id="08854-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="08854-395">Poniższy kod dodaje *MySubsection.json* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="08854-396">Getsection</span><span class="sxs-lookup"><span data-stu-id="08854-396">GetSection</span></span>

<span data-ttu-id="08854-397">[Program IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="08854-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="08854-398">Następujący kod zwraca `section1`wartości dla:</span><span class="sxs-lookup"><span data-stu-id="08854-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-399">Następujący kod zwraca `section2:subsection0`wartości dla:</span><span class="sxs-lookup"><span data-stu-id="08854-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-400">`GetSection`nigdy `null`nie zwraca .</span><span class="sxs-lookup"><span data-stu-id="08854-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="08854-401">Jeśli pasująca sekcja nie zostanie `IConfigurationSection` znaleziona, zwracana jest pusta sekcja.</span><span class="sxs-lookup"><span data-stu-id="08854-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="08854-402">Po `GetSection` zwróceniu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> pasującej sekcji nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="08854-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="08854-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> i są zwracane, gdy sekcja istnieje.</span><span class="sxs-lookup"><span data-stu-id="08854-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="08854-404">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="08854-404">GetChildren and Exists</span></span>

<span data-ttu-id="08854-405">Poniższy kod wywołuje [iConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) `section2:subsection0`i zwraca wartości dla:</span><span class="sxs-lookup"><span data-stu-id="08854-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-406">Poprzedni kod wywołuje [ConfigurationExtensions.Exists,](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) aby sprawdzić, czy istnieje sekcja:</span><span class="sxs-lookup"><span data-stu-id="08854-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="08854-407">Powiązyj tablicę</span><span class="sxs-lookup"><span data-stu-id="08854-407">Bind an array</span></span>

<span data-ttu-id="08854-408">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązania do obiektów przy użyciu indeksów tablic w kluczach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="08854-409">Każdy format tablicy, który udostępnia segment klucza numerycznego jest w stanie powiązania tablicy do tablicy klasy [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)</span><span class="sxs-lookup"><span data-stu-id="08854-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="08854-410">Rozważmy *MyArray.json* z [próbki do pobrania:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)</span><span class="sxs-lookup"><span data-stu-id="08854-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="08854-411">Poniższy kod dodaje *MyArray.json* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="08854-412">Następujący kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="08854-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-413">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="08854-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="08854-414">W poprzednim wyjściu indeks 3 `value40`ma `"4": "value40",` wartość odpowiadającą *w myarray.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="08854-415">Indeksy tablicy powiązanej są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="08854-416">Spinacza konfiguracji nie może wiązać wartości null lub tworzyć wpisy null w obiektach powiązanych</span><span class="sxs-lookup"><span data-stu-id="08854-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="08854-417">Następujący kod ładuje `array:entries` konfigurację <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> za pomocą metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="08854-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="08854-418">Poniższy kod odczytuje `arrayDict` `Dictionary` konfigurację w i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="08854-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-419">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="08854-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="08854-420">Indeks &num;3 w obiekcie powiązanym `array:4` przechowuje dane konfiguracyjne klucza konfiguracji i jego wartość `value4`.</span><span class="sxs-lookup"><span data-stu-id="08854-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="08854-421">Gdy dane konfiguracyjne zawierające tablicę są powiązane, indeksy tablicowe w kluczach konfiguracyjnych są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="08854-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="08854-422">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracyjnych pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="08854-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="08854-423">Brakujący element konfiguracji &num;dla indeksu 3 `ArrayExample` może być dostarczony przed powiązaniem z wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje parę klucz/wartość indeksu &num;3.</span><span class="sxs-lookup"><span data-stu-id="08854-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="08854-424">Należy wziąć pod uwagę następujący plik *Value3.json* z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="08854-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="08854-425">Poniższy kod zawiera konfigurację *dla value3.json* `arrayDict` `Dictionary`i:</span><span class="sxs-lookup"><span data-stu-id="08854-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="08854-426">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="08854-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="08854-427">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="08854-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="08854-428">Dostawcy konfiguracji niestandardowej nie są wymagane do zaimplementowania powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="08854-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="08854-429">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-429">Custom configuration provider</span></span>

<span data-ttu-id="08854-430">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucza konfiguracji z bazy danych przy użyciu [entity framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="08854-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="08854-431">Dostawca posiada następujące cechy:</span><span class="sxs-lookup"><span data-stu-id="08854-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="08854-432">Baza danych EF w pamięci jest używana do celów demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="08854-433">Aby użyć bazy danych, która wymaga `ConfigurationBuilder` ciągu połączenia, należy zaimplementować pomocniczy do poniesienia ciągu połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="08854-434">Dostawca odczytuje tabelę bazy danych do konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="08854-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="08854-435">Dostawca nie wysyła zapytania do bazy danych na podstawie klucza.</span><span class="sxs-lookup"><span data-stu-id="08854-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="08854-436">Ponowne ładowanie na zmianę nie jest zaimplementowana, więc aktualizowanie bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="08854-437">Zdefiniuj encję `EFConfigurationValue` do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="08854-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="08854-438">*Modele/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="08854-439">Dodaj `EFConfigurationContext` do przechowywania i dostępu do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="08854-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="08854-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="08854-441">Utwórz klasę implementuują . <xref:Microsoft.Extensions.Configuration.IConfigurationSource></span><span class="sxs-lookup"><span data-stu-id="08854-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="08854-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="08854-443">Utwórz niestandardowego dostawcę <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>konfiguracji, dziedzicząc po programie .</span><span class="sxs-lookup"><span data-stu-id="08854-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="08854-444">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="08854-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="08854-445">Ponieważ [klucze konfiguracyjne są niewrażliwe na rozmiary,](#keys)słownik używany do inicjowania bazy danych jest tworzony za pomocą modułu porównującego bez uwzględniania wielkości liter ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="08854-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="08854-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="08854-447">Metoda `AddEFConfiguration` rozszerzenia umożliwia dodanie źródła `ConfigurationBuilder`konfiguracji do pliku .</span><span class="sxs-lookup"><span data-stu-id="08854-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="08854-448">*Rozszerzenia/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="08854-449">Poniższy kod pokazuje, jak `EFConfigurationProvider` używać niestandardowych w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="08854-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="08854-450">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="08854-450">Access configuration in Startup</span></span>

<span data-ttu-id="08854-451">Następujący kod wyświetla dane `Startup` konfiguracyjne w metodach:</span><span class="sxs-lookup"><span data-stu-id="08854-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="08854-452">Na przykład uzyskiwania dostępu do konfiguracji przy użyciu metod wygody uruchamiania, zobacz [Uruchamianie aplikacji: Metody wygody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="08854-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="08854-453">Konfiguracja dostępu na stronach Razor</span><span class="sxs-lookup"><span data-stu-id="08854-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="08854-454">Następujący kod wyświetla dane konfiguracyjne na stronie Razor:</span><span class="sxs-lookup"><span data-stu-id="08854-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="08854-455">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="08854-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="08854-456">Następujący kod wyświetla dane konfiguracyjne w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="08854-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="08854-457">Konfiguracja hosta i aplikacji</span><span class="sxs-lookup"><span data-stu-id="08854-457">Host versus app configuration</span></span>

<span data-ttu-id="08854-458">Przed skonfigurowaniem i uruchomieniem aplikacji *host* jest konfigurowany i uruchamiany.</span><span class="sxs-lookup"><span data-stu-id="08854-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="08854-459">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="08854-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="08854-460">Zarówno aplikacja, jak i host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="08854-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="08854-461">Pary klucza-wartość konfiguracji hosta są również uwzględniane w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="08854-462">Aby uzyskać więcej informacji na temat sposobu, w jaki dostawcy konfiguracji są <xref:fundamentals/index#host>używane podczas budowy hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz .</span><span class="sxs-lookup"><span data-stu-id="08854-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="08854-463">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="08854-463">Default host configuration</span></span>

<span data-ttu-id="08854-464">Szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host)można znaleźć w [ASP.NET wersji Core 2.2 w tym temacie](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="08854-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="08854-465">Konfiguracja hosta jest dostarczana z:</span><span class="sxs-lookup"><span data-stu-id="08854-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="08854-466">Zmienne środowiskowe poprzedzone `DOTNET_` (na `DOTNET_ENVIRONMENT`przykład) przy użyciu dostawcy konfiguracji [Zmienne środowiskowe](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="08854-467">Prefiks`DOTNET_`( ) jest usuwany po załadowaniu par klucza konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="08854-468">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="08854-469">Domyślna konfiguracja hosta`ConfigureWebHostDefaults`sieci Web jest ustanawiana ( ):</span><span class="sxs-lookup"><span data-stu-id="08854-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="08854-470">Pustułka jest używana jako serwer www i konfigurowana przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="08854-471">Dodaj oprogramowanie pośredniczące filtrowania hostów.</span><span class="sxs-lookup"><span data-stu-id="08854-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="08854-472">Dodaj oprogramowanie pośredniczące nagłówków przesyłanych dalej, jeśli zmienna środowiskowa `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest ustawiona na `true`.</span><span class="sxs-lookup"><span data-stu-id="08854-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="08854-473">Włącz integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="08854-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="08854-474">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="08854-474">Other configuration</span></span>

<span data-ttu-id="08854-475">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="08854-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="08854-476">Inne aspekty uruchamiania i hostingu aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracyjnych, które nie zostały omówione w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="08854-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="08854-477">*launch.json*/*launchSettings.json* są oprzyrządowanie plików konfiguracyjnych dla środowiska programistycznego, opisane:</span><span class="sxs-lookup"><span data-stu-id="08854-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="08854-478">W <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="08854-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="08854-479">W całej dokumentacji zestaw, gdzie pliki są używane do konfigurowania ASP.NET podstawowe aplikacje dla scenariuszy rozwoju.</span><span class="sxs-lookup"><span data-stu-id="08854-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="08854-480">*web.config* to plik konfiguracyjny serwera, opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="08854-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="08854-481">Aby uzyskać więcej informacji na temat migracji konfiguracji aplikacji <xref:migration/proper-to-2x/index#store-configurations>z wcześniejszych wersji ASP.NET, zobacz .</span><span class="sxs-lookup"><span data-stu-id="08854-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="08854-482">Dodawanie konfiguracji z zespołu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="08854-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="08854-483">Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania `Startup` z zewnętrznego zestawu poza klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="08854-484">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="08854-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08854-485">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="08854-485">Additional resources</span></span>

* [<span data-ttu-id="08854-486">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08854-487">Konfiguracja aplikacji w ASP.NET Core opiera się na parach klucz-wartość ustanowiona przez *dostawców konfiguracji.*</span><span class="sxs-lookup"><span data-stu-id="08854-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="08854-488">Dostawcy konfiguracji odczytywać dane konfiguracyjne do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="08854-489">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="08854-489">Azure Key Vault</span></span>
* <span data-ttu-id="08854-490">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="08854-490">Azure App Configuration</span></span>
* <span data-ttu-id="08854-491">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-491">Command-line arguments</span></span>
* <span data-ttu-id="08854-492">Dostawcy niestandardowi (zainstalowane lub utworzone)</span><span class="sxs-lookup"><span data-stu-id="08854-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="08854-493">Pliki katalogów</span><span class="sxs-lookup"><span data-stu-id="08854-493">Directory files</span></span>
* <span data-ttu-id="08854-494">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="08854-494">Environment variables</span></span>
* <span data-ttu-id="08854-495">Obiekty NET w pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-495">In-memory .NET objects</span></span>
* <span data-ttu-id="08854-496">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="08854-496">Settings files</span></span>

<span data-ttu-id="08854-497">Pakiety konfiguracyjne dla typowych scenariuszy dostawców konfiguracji ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="08854-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="08854-498">Przykłady kodu, które należy wykonać <xref:Microsoft.Extensions.Configuration> i w przykładowej aplikacji używać obszaru nazw:</span><span class="sxs-lookup"><span data-stu-id="08854-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="08854-499">*Wzorzec opcji* jest rozszerzeniem koncepcji konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="08854-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="08854-500">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="08854-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="08854-501">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="08854-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="08854-502">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08854-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="08854-503">Konfiguracja hosta i aplikacji</span><span class="sxs-lookup"><span data-stu-id="08854-503">Host versus app configuration</span></span>

<span data-ttu-id="08854-504">Przed skonfigurowaniem i uruchomieniem aplikacji *host* jest konfigurowany i uruchamiany.</span><span class="sxs-lookup"><span data-stu-id="08854-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="08854-505">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="08854-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="08854-506">Zarówno aplikacja, jak i host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="08854-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="08854-507">Pary klucza-wartość konfiguracji hosta są również uwzględniane w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="08854-508">Aby uzyskać więcej informacji na temat sposobu, w jaki dostawcy konfiguracji są <xref:fundamentals/index#host>używane podczas budowy hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz .</span><span class="sxs-lookup"><span data-stu-id="08854-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="08854-509">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="08854-509">Other configuration</span></span>

<span data-ttu-id="08854-510">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="08854-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="08854-511">Inne aspekty uruchamiania i hostingu aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracyjnych, które nie zostały omówione w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="08854-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="08854-512">*launch.json*/*launchSettings.json* są oprzyrządowanie plików konfiguracyjnych dla środowiska programistycznego, opisane:</span><span class="sxs-lookup"><span data-stu-id="08854-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="08854-513">W <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="08854-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="08854-514">W całej dokumentacji zestaw, gdzie pliki są używane do konfigurowania ASP.NET podstawowe aplikacje dla scenariuszy rozwoju.</span><span class="sxs-lookup"><span data-stu-id="08854-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="08854-515">*web.config* to plik konfiguracyjny serwera, opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="08854-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="08854-516">Aby uzyskać więcej informacji na temat migracji konfiguracji aplikacji <xref:migration/proper-to-2x/index#store-configurations>z wcześniejszych wersji ASP.NET, zobacz .</span><span class="sxs-lookup"><span data-stu-id="08854-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="08854-517">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="08854-517">Default configuration</span></span>

<span data-ttu-id="08854-518">Aplikacje sieci Web oparte na ASP.NET Core [dotnet nowe](/dotnet/core/tools/dotnet-new) szablony wywołać <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas tworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="08854-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="08854-519">`CreateDefaultBuilder`zapewnia domyślną konfigurację aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="08854-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="08854-520">Poniżej przedstawiono aplikacje korzystające z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="08854-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="08854-521">Aby uzyskać szczegółowe informacje na temat domyślnej konfiguracji podczas korzystania z [hosta ogólnego,](xref:fundamentals/host/generic-host)zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="08854-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="08854-522">Konfiguracja hosta jest dostarczana z:</span><span class="sxs-lookup"><span data-stu-id="08854-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="08854-523">Zmienne środowiskowe poprzedzone `ASPNETCORE_` (na `ASPNETCORE_ENVIRONMENT`przykład) przy użyciu dostawcy konfiguracji [zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="08854-524">Prefiks`ASPNETCORE_`( ) jest usuwany po załadowaniu par klucza konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="08854-525">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="08854-526">Konfiguracja aplikacji jest dostarczana z:</span><span class="sxs-lookup"><span data-stu-id="08854-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="08854-527">*appsettings.json* za pomocą [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="08854-528">*appsettings. {Środowisko}.json* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="08854-529">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku przy użyciu zestawu wejścia.</span><span class="sxs-lookup"><span data-stu-id="08854-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="08854-530">Zmienne środowiskowe przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="08854-531">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="08854-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="08854-532">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="08854-532">Security</span></span>

<span data-ttu-id="08854-533">Przyjęcie następujących rozwiązań w celu zabezpieczenia poufnych danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="08854-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="08854-534">Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie dostawcy konfiguracji lub w plikach konfiguracyjnych w postaci zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="08854-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="08854-535">Nie używaj wpisów tajnych produkcji w środowiskach deweloperskich lub testowych.</span><span class="sxs-lookup"><span data-stu-id="08854-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="08854-536">Określ wpisy tajne poza projektem, tak aby nie można przypadkowo zatwierdzone do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="08854-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="08854-537">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="08854-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="08854-538"><xref:security/app-secrets>&ndash; Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="08854-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="08854-539">Menedżer tajny używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="08854-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="08854-540">Dostawca konfiguracji plików jest opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="08854-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="08854-541">[Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpiecznie przechowuje wpisy tajne aplikacji dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08854-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="08854-542">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="08854-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="08854-543">Hierarchiczne dane konfiguracyjne</span><span class="sxs-lookup"><span data-stu-id="08854-543">Hierarchical configuration data</span></span>

<span data-ttu-id="08854-544">Interfejs API konfiguracji jest w stanie obsługi danych konfiguracji hierarchicznej przez spłaszczenie danych hierarchicznych za pomocą ogranicznika w kluczach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="08854-545">W następującym pliku JSON istnieją cztery klucze w ustrukturyzowanej hierarchii dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="08854-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="08854-546">Gdy plik jest odczytywany do konfiguracji, unikatowe klucze są tworzone w celu utrzymania oryginalnej hierarchicznej struktury danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="08854-547">Sekcje i klawisze są spłaszczane za`:`pomocą dwukropka ( ) w celu utrzymania oryginalnej struktury:</span><span class="sxs-lookup"><span data-stu-id="08854-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="08854-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="08854-548">section0:key0</span></span>
* <span data-ttu-id="08854-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="08854-549">section0:key1</span></span>
* <span data-ttu-id="08854-550">sekcja1:key0</span><span class="sxs-lookup"><span data-stu-id="08854-550">section1:key0</span></span>
* <span data-ttu-id="08854-551">sekcja1:key1</span><span class="sxs-lookup"><span data-stu-id="08854-551">section1:key1</span></span>

<span data-ttu-id="08854-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>i <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody są dostępne do izolowania sekcji i obrażeń części sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="08854-553">Metody te są opisane później w [GetSection, GetChildren i Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="08854-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="08854-554">Konwencja</span><span class="sxs-lookup"><span data-stu-id="08854-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="08854-555">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="08854-555">Sources and providers</span></span>

<span data-ttu-id="08854-556">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w yszczególnie ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="08854-557">Dostawcy konfiguracji, którzy implementują wykrywanie zmian, mają możliwość ponownego ładowania konfiguracji po zmianie ustawienia źródłowego.</span><span class="sxs-lookup"><span data-stu-id="08854-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="08854-558">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji usługi Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="08854-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="08854-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>jest dostępna w kontenerze [iniekcji zależności aplikacji (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="08854-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="08854-560"><xref:Microsoft.Extensions.Configuration.IConfiguration>można wstrzyknąć do <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> strony <xref:Microsoft.AspNetCore.Mvc.Controller> Razor lub MVC w celu uzyskania konfiguracji dla klasy.</span><span class="sxs-lookup"><span data-stu-id="08854-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="08854-561">W poniższych przykładach `_config` pole jest używane do uzyskiwania dostępu do wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="08854-562">Dostawcy konfiguracji nie mogą korzystać z DI, ponieważ nie jest on dostępny, gdy są konfiguracyjnie przez hosta.</span><span class="sxs-lookup"><span data-stu-id="08854-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="08854-563">Klucze</span><span class="sxs-lookup"><span data-stu-id="08854-563">Keys</span></span>

<span data-ttu-id="08854-564">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="08854-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="08854-565">Klucze są niewrażliwe na wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="08854-565">Keys are case-insensitive.</span></span> <span data-ttu-id="08854-566">Na przykład `ConnectionString` `connectionstring` i są traktowane jako klucze równoważne.</span><span class="sxs-lookup"><span data-stu-id="08854-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="08854-567">Jeśli wartość dla tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, ostatnią wartością ustawioną na klucz jest używana wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="08854-568">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="08854-568">Hierarchical keys</span></span>
  * <span data-ttu-id="08854-569">W interfejsie API konfiguracji separator dwukropka (`:`) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="08854-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="08854-570">W zmiennych środowiskowych separator dwukropek może nie działać na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="08854-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="08854-571">Podwójne podkreślenie (`__`) jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="08854-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="08854-572">W usłudze Azure Key `--` Vault klucze hierarchiczne używają (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="08854-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="08854-573">Napisz kod, aby zastąpić kreski dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="08854-574">Obsługuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> tablice powiązania do obiektów przy użyciu indeksów tablicy w kluczach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="08854-575">Powiązanie tablicy jest opisane w [Bind tablicy do](#bind-an-array-to-a-class) sekcji klasy.</span><span class="sxs-lookup"><span data-stu-id="08854-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="08854-576">Wartości</span><span class="sxs-lookup"><span data-stu-id="08854-576">Values</span></span>

<span data-ttu-id="08854-577">Wartości konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="08854-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="08854-578">Wartości są ciągami znaków.</span><span class="sxs-lookup"><span data-stu-id="08854-578">Values are strings.</span></span>
* <span data-ttu-id="08854-579">Wartości null nie mogą być przechowywane w konfiguracji lub powiązane z obiektami.</span><span class="sxs-lookup"><span data-stu-id="08854-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="08854-580">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="08854-580">Providers</span></span>

<span data-ttu-id="08854-581">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET aplikacji Core.</span><span class="sxs-lookup"><span data-stu-id="08854-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="08854-582">Dostawca</span><span class="sxs-lookup"><span data-stu-id="08854-582">Provider</span></span> | <span data-ttu-id="08854-583">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="08854-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="08854-584">[Dostawca konfiguracji usługi Azure Key Vault](xref:security/key-vault-configuration) (zagadnienia dotyczące*zabezpieczeń)*</span><span class="sxs-lookup"><span data-stu-id="08854-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="08854-585">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="08854-585">Azure Key Vault</span></span> |
| <span data-ttu-id="08854-586">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="08854-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="08854-587">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="08854-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="08854-588">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="08854-589">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-589">Command-line parameters</span></span> |
| [<span data-ttu-id="08854-590">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="08854-591">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="08854-591">Custom source</span></span> |
| [<span data-ttu-id="08854-592">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="08854-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="08854-593">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="08854-593">Environment variables</span></span> |
| [<span data-ttu-id="08854-594">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="08854-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="08854-595">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="08854-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="08854-596">Dostawca konfiguracji klucza na plik</span><span class="sxs-lookup"><span data-stu-id="08854-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="08854-597">Pliki katalogów</span><span class="sxs-lookup"><span data-stu-id="08854-597">Directory files</span></span> |
| [<span data-ttu-id="08854-598">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="08854-599">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-599">In-memory collections</span></span> |
| <span data-ttu-id="08854-600">[Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) ( Tematy*zabezpieczeń)*</span><span class="sxs-lookup"><span data-stu-id="08854-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="08854-601">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="08854-601">File in the user profile directory</span></span> |

<span data-ttu-id="08854-602">Źródła konfiguracji są odczytywane w kolejności, w ich dostawców konfiguracji są określone podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="08854-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="08854-603">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w porządku, że kod organizuje je.</span><span class="sxs-lookup"><span data-stu-id="08854-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="08854-604">Dostawców konfiguracji zamówienia w kodzie, aby dopasować priorytety dla podstawowych źródeł konfiguracji, które wymaga aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="08854-605">Typowa sekwencja dostawców konfiguracji to:</span><span class="sxs-lookup"><span data-stu-id="08854-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="08854-606">Pliki (*appsettings.json*, *appsettings.{ Środowisko}.json*, `{Environment}` gdzie jest bieżące środowisko hostingowe aplikacji)</span><span class="sxs-lookup"><span data-stu-id="08854-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="08854-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="08854-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="08854-608">[Wpisy tajne użytkownika (secret manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="08854-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="08854-609">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="08854-609">Environment variables</span></span>
1. <span data-ttu-id="08854-610">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-610">Command-line arguments</span></span>

<span data-ttu-id="08854-611">Powszechną praktyką jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatniego w serii dostawców, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji ustawionej przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="08854-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="08854-612">Poprzednia sekwencja dostawców jest używana, gdy nowy konstruktor hosta jest inicjowany za pomocą pliku `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="08854-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="08854-613">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="08854-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="08854-614">Konfigurowanie konstruktora hosta za pomocą funkcji UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="08854-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="08854-615">Aby skonfigurować konstruktora <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> hosta, wywołaj konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="08854-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="08854-616">Konfigurowanie konfiguracjiappconfiguration</span><span class="sxs-lookup"><span data-stu-id="08854-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="08854-617">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić dostawców konfiguracji aplikacji `CreateDefaultBuilder`oprócz tych dodawanych automatycznie przez:</span><span class="sxs-lookup"><span data-stu-id="08854-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="08854-618">Zastępowanie poprzedniej konfiguracji argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="08854-619">Aby zapewnić konfigurację aplikacji, którą można zastąpić argumentami wiersza polecenia, zadzwoń `AddCommandLine` jako ostatni:</span><span class="sxs-lookup"><span data-stu-id="08854-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="08854-620">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="08854-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="08854-621">Aby usunąć dostawców `CreateDefaultBuilder`dodanych przez , najpierw wywołaj [clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [iConfigurationBuilder.Sources:](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)</span><span class="sxs-lookup"><span data-stu-id="08854-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="08854-622">Korzystanie z konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="08854-622">Consume configuration during app startup</span></span>

<span data-ttu-id="08854-623">Konfiguracja dostarczona `ConfigureAppConfiguration` do aplikacji jest dostępna podczas `Startup.ConfigureServices`uruchamiania aplikacji, w tym .</span><span class="sxs-lookup"><span data-stu-id="08854-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="08854-624">Aby uzyskać więcej informacji, zobacz [sekcję Konfiguracja programu Access podczas uruchamiania.](#access-configuration-during-startup)</span><span class="sxs-lookup"><span data-stu-id="08854-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="08854-625">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="08854-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="08854-626">Konfiguracja <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="08854-627">Aby aktywować konfigurację <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> wiersza polecenia, metoda <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>rozszerzenia jest wywoływana w wystąpieniu programu .</span><span class="sxs-lookup"><span data-stu-id="08854-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="08854-628">`AddCommandLine`jest wywoływana `CreateDefaultBuilder(string [])` automatycznie, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="08854-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="08854-629">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="08854-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="08854-630">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="08854-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="08854-631">Opcjonalna konfiguracja z *pliku appsettings.json* i *appsettings.{ Środowiska}.json.*</span><span class="sxs-lookup"><span data-stu-id="08854-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="08854-632">[Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="08854-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="08854-633">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="08854-633">Environment variables.</span></span>

<span data-ttu-id="08854-634">`CreateDefaultBuilder`dodaje jako ostatniego dostawcę konfiguracji wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="08854-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="08854-635">Argumenty wiersza polecenia przekazywane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="08854-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="08854-636">`CreateDefaultBuilder`działa, gdy host jest konstruowany.</span><span class="sxs-lookup"><span data-stu-id="08854-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="08854-637">W związku z tym konfiguracja `CreateDefaultBuilder` wiersza polecenia aktywowana przez może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="08854-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="08854-638">W przypadku aplikacji opartych na szablonach `AddCommandLine` ASP.NET Core `CreateDefaultBuilder`został już wywołany przez program .</span><span class="sxs-lookup"><span data-stu-id="08854-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="08854-639">Aby dodać dodatkowych dostawców konfiguracji i zachować możliwość zastąpienia konfiguracji od tych dostawców z argumentami `ConfigureAppConfiguration` wiersza polecenia, zadzwoń do dodatkowych dostawców aplikacji i wywołaj `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="08854-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="08854-640">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="08854-640">**Example**</span></span>

<span data-ttu-id="08854-641">Przykładowa aplikacja korzysta z statycznej `CreateDefaultBuilder` metody wygody do tworzenia <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>hosta, która zawiera wywołanie .</span><span class="sxs-lookup"><span data-stu-id="08854-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="08854-642">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="08854-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="08854-643">Podaj argument wiersza `dotnet run` polecenia `dotnet run CommandLineKey=CommandLineValue`do polecenia , .</span><span class="sxs-lookup"><span data-stu-id="08854-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="08854-644">Po uruchomieniu aplikacji otwórz przeglądarkę w `http://localhost:5000`aplikacji pod adresem .</span><span class="sxs-lookup"><span data-stu-id="08854-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="08854-645">Należy zauważyć, że dane wyjściowe zawierają parę klucz-wartość `dotnet run`dla argumentu wiersza polecenia konfiguracji dostarczonego do .</span><span class="sxs-lookup"><span data-stu-id="08854-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="08854-646">Argumenty</span><span class="sxs-lookup"><span data-stu-id="08854-646">Arguments</span></span>

<span data-ttu-id="08854-647">Wartość musi być zgodna ze`=`znakiem równości ( ) lub`--` `/`klucz musi mieć prefiks ( lub ), gdy wartość podąża za spacją.</span><span class="sxs-lookup"><span data-stu-id="08854-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="08854-648">Wartość nie jest wymagana, jeśli używany jest znak równy (na przykład `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="08854-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="08854-649">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="08854-649">Key prefix</span></span>               | <span data-ttu-id="08854-650">Przykład</span><span class="sxs-lookup"><span data-stu-id="08854-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="08854-651">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="08854-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="08854-652">Dwie kreski`--`( )</span><span class="sxs-lookup"><span data-stu-id="08854-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="08854-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="08854-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="08854-654">Ukośnik do przodu (`/`)</span><span class="sxs-lookup"><span data-stu-id="08854-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="08854-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="08854-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="08854-656">W ramach tego samego polecenia nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="08854-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="08854-657">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="08854-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="08854-658">Mapowania przełączników</span><span class="sxs-lookup"><span data-stu-id="08854-658">Switch mappings</span></span>

<span data-ttu-id="08854-659">Mapowania przełączników umożliwiają logikę zastępowania nazw kluczy.</span><span class="sxs-lookup"><span data-stu-id="08854-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="08854-660">Podczas ręcznego budowania <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>konfiguracji za pomocą programu , <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> należy podać słownik zamienników przełączników do metody.</span><span class="sxs-lookup"><span data-stu-id="08854-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="08854-661">Gdy używany jest słownik mapowania przełącznika, słownik jest sprawdzany pod kątem klucza zgodnego z kluczem dostarczonym przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="08854-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="08854-662">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (zastąpienie klucza) jest przekazywana z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="08854-663">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza`-`polecenia poprzedzonym pojedynczą kreską ( ).</span><span class="sxs-lookup"><span data-stu-id="08854-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="08854-664">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="08854-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="08854-665">Przełączniki muszą zaczynać`-`się od kreski`--`( ) lub podwójnej kreski ( ).</span><span class="sxs-lookup"><span data-stu-id="08854-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="08854-666">Słownik mapowań przełączników nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="08854-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="08854-667">Tworzenie słownika mapowań przełączników.</span><span class="sxs-lookup"><span data-stu-id="08854-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="08854-668">W poniższym przykładzie tworzone są dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="08854-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="08854-669">Po zbudowaniu hosta wywołaj ze `AddCommandLine` słownikiem mapowań przełączników:</span><span class="sxs-lookup"><span data-stu-id="08854-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="08854-670">W przypadku aplikacji korzystających z `CreateDefaultBuilder` mapowań przełączników wywołanie nie powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="08854-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="08854-671">Wywołanie metody nie zawiera mapowanych przełączników i nie ma możliwości przekazania słownika `CreateDefaultBuilder`mapowania przełącznika do . `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="08854-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="08854-672">Rozwiązanie nie jest przekazywanie argumentów, `CreateDefaultBuilder` ale zamiast `ConfigurationBuilder` tego, `AddCommandLine` aby umożliwić metody do przetwarzania zarówno argumenty i słownik mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="08854-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="08854-673">Po utworzeniu słownika mapowania przełączników zawiera on dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="08854-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="08854-674">Klucz</span><span class="sxs-lookup"><span data-stu-id="08854-674">Key</span></span>       | <span data-ttu-id="08854-675">Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="08854-676">Jeśli podczas uruchamiania aplikacji są używane klucze mapowane przez przełączniki, konfiguracja odbiera wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="08854-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="08854-677">Po uruchomieniu poprzedniego polecenia konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="08854-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="08854-678">Klucz</span><span class="sxs-lookup"><span data-stu-id="08854-678">Key</span></span>               | <span data-ttu-id="08854-679">Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="08854-680">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="08854-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="08854-681">Konfiguracja <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> obciążeń z par klucza klucza-wartości zmiennej środowiskowej w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="08854-682">Aby aktywować konfigurację zmiennych środowiskowych, należy wywołać metodę <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="08854-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="08854-683">[Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) zezwala na ustawianie zmiennych środowiskowych w portalu Azure Portal, które mogą zastępować konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="08854-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="08854-684">Aby uzyskać więcej informacji, zobacz [Usługi Azure Apps: Zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="08854-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="08854-685">`AddEnvironmentVariables`służy do ładowania zmiennych środowiskowych `ASPNETCORE_` poprzedzonych konfiguracją [hosta,](#host-versus-app-configuration) gdy nowy konstruktor hosta jest inicjowany za pomocą [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="08854-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="08854-686">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="08854-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="08854-687">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="08854-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="08854-688">Konfiguracja aplikacji z nieprefikowanych `AddEnvironmentVariables` zmiennych środowiskowych przez wywołanie bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="08854-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="08854-689">Opcjonalna konfiguracja z *pliku appsettings.json* i *appsettings.{ Środowiska}.json.*</span><span class="sxs-lookup"><span data-stu-id="08854-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="08854-690">[Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="08854-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="08854-691">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="08854-691">Command-line arguments.</span></span>

<span data-ttu-id="08854-692">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z plików *wpisów tajnych i appsettings* użytkownika.</span><span class="sxs-lookup"><span data-stu-id="08854-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="08854-693">Wywołanie dostawcy w tej pozycji umożliwia zmienne środowiskowe odczytywane w czasie wykonywania, aby zastąpić konfigurację ustawioną przez wpisy tajne użytkownika i pliki *appsettings.*</span><span class="sxs-lookup"><span data-stu-id="08854-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="08854-694">Aby zapewnić konfigurację aplikacji z dodatkowych zmiennych środowiskowych, `ConfigureAppConfiguration` zadzwoń `AddEnvironmentVariables` do dodatkowych dostawców aplikacji i zadzwoń z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="08854-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="08854-695">Wywołanie `AddEnvironmentVariables` ostatni, aby umożliwić zmienne środowiskowe z danym prefiksem, aby zastąpić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="08854-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="08854-696">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="08854-696">**Example**</span></span>

<span data-ttu-id="08854-697">Przykładowa aplikacja korzysta z statycznej `CreateDefaultBuilder` metody wygody do tworzenia `AddEnvironmentVariables`hosta, która zawiera wywołanie .</span><span class="sxs-lookup"><span data-stu-id="08854-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="08854-698">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="08854-698">Run the sample app.</span></span> <span data-ttu-id="08854-699">Otwórz przeglądarkę w `http://localhost:5000`aplikacji pod adresem .</span><span class="sxs-lookup"><span data-stu-id="08854-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="08854-700">Należy zauważyć, że dane wyjściowe zawierają `ENVIRONMENT`parę klucz-wartość dla zmiennej środowiskowej .</span><span class="sxs-lookup"><span data-stu-id="08854-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="08854-701">Wartość odzwierciedla środowisko, w którym aplikacja jest `Development` uruchomiona, zazwyczaj podczas uruchamiania lokalnie.</span><span class="sxs-lookup"><span data-stu-id="08854-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="08854-702">Aby zachować listę zmiennych środowiskowych renderowane przez aplikację krótki, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="08854-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="08854-703">Zobacz przykładowy plik *Pages/Index.cshtml.cs* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="08854-704">Aby udostępnić wszystkie zmienne środowiskowe dostępne `FilteredConfiguration` dla aplikacji, zmień w *pages/index.cshtml.cs* na następujące:</span><span class="sxs-lookup"><span data-stu-id="08854-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="08854-705">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="08854-705">Prefixes</span></span>

<span data-ttu-id="08854-706">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane `AddEnvironmentVariables` podczas dostarczania prefiksu do metody.</span><span class="sxs-lookup"><span data-stu-id="08854-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="08854-707">Na przykład, aby filtrować zmienne `CUSTOM_`środowiskowe w prefiksie, podaj prefiks dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="08854-708">Prefiks jest usuwany podczas tworzenia par klucza konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="08854-709">Po utworzeniu konstruktora hosta konfiguracja hosta jest dostarczana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="08854-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="08854-710">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="08854-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="08854-711">**Prefiksy ciągu połączenia**</span><span class="sxs-lookup"><span data-stu-id="08854-711">**Connection string prefixes**</span></span>

<span data-ttu-id="08854-712">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych ciągu połączenia zaangażowanych w konfigurowanie ciągów połączeń platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="08854-713">Zmienne środowiskowe z prefiksami pokazanymi w tabeli są ładowane do aplikacji, jeśli do `AddEnvironmentVariables`pliku .</span><span class="sxs-lookup"><span data-stu-id="08854-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="08854-714">Prefiks ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="08854-714">Connection string prefix</span></span> | <span data-ttu-id="08854-715">Dostawca</span><span class="sxs-lookup"><span data-stu-id="08854-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="08854-716">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="08854-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="08854-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="08854-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="08854-718">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="08854-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="08854-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="08854-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="08854-720">Po wykryciu i załadowaniu zmiennej środowiskowej do konfiguracji z dowolnym z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="08854-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="08854-721">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej`ConnectionStrings`środowiskowej i dodanie sekcji klucza konfiguracji ( ).</span><span class="sxs-lookup"><span data-stu-id="08854-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="08854-722">Tworzona jest nowa para klucza konfiguracji i wartość `CUSTOMCONNSTR_`reprezentująca dostawcę połączenia bazy danych (z wyjątkiem , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="08854-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="08854-723">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="08854-723">Environment variable key</span></span> | <span data-ttu-id="08854-724">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-724">Converted configuration key</span></span> | <span data-ttu-id="08854-725">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="08854-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-726">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="08854-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-727">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="08854-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="08854-728">Wartość:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="08854-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-729">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="08854-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="08854-730">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="08854-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="08854-731">Klucz: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="08854-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="08854-732">Wartość:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="08854-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="08854-733">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="08854-733">**Example**</span></span>

<span data-ttu-id="08854-734">Na serwerze tworzona jest niestandardowa zmienna środowiskowa ciągu połączenia:</span><span class="sxs-lookup"><span data-stu-id="08854-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="08854-735">Imię &ndash; i nazwisko`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="08854-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="08854-736">Wartość &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="08854-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="08854-737">Jeśli `IConfiguration` jest wstrzykiwany i przypisany `_config`do pola o nazwie, przeczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="08854-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="08854-738">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="08854-738">File Configuration Provider</span></span>

<span data-ttu-id="08854-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą podstawową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="08854-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="08854-740">Następujący dostawcy konfiguracji są dedykowane do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="08854-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="08854-741">Dostawca konfiguracji INI</span><span class="sxs-lookup"><span data-stu-id="08854-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="08854-742">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="08854-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="08854-743">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="08854-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="08854-744">Dostawca konfiguracji INI</span><span class="sxs-lookup"><span data-stu-id="08854-744">INI Configuration Provider</span></span>

<span data-ttu-id="08854-745">Konfiguracja <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> ładowania z par klucza klucza-wartość pliku INI w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="08854-746">Aby aktywować konfigurację pliku <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> INI, wywołaj metodę rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="08854-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="08854-747">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="08854-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="08854-748">Przeciążenia pozwalają na określenie:</span><span class="sxs-lookup"><span data-stu-id="08854-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="08854-749">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="08854-749">Whether the file is optional.</span></span>
* <span data-ttu-id="08854-750">Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.</span><span class="sxs-lookup"><span data-stu-id="08854-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="08854-751">Używany <xref:Microsoft.Extensions.FileProviders.IFileProvider> do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="08854-752">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="08854-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="08854-753">Ogólny przykład pliku konfiguracyjnego INI:</span><span class="sxs-lookup"><span data-stu-id="08854-753">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="08854-754">Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`</span><span class="sxs-lookup"><span data-stu-id="08854-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="08854-755">section0:key0</span><span class="sxs-lookup"><span data-stu-id="08854-755">section0:key0</span></span>
* <span data-ttu-id="08854-756">section0:key1</span><span class="sxs-lookup"><span data-stu-id="08854-756">section0:key1</span></span>
* <span data-ttu-id="08854-757">section1:podsekcja:klucz</span><span class="sxs-lookup"><span data-stu-id="08854-757">section1:subsection:key</span></span>
* <span data-ttu-id="08854-758">section2:podsekcja0:klucz</span><span class="sxs-lookup"><span data-stu-id="08854-758">section2:subsection0:key</span></span>
* <span data-ttu-id="08854-759">section2:podsekcja1:klucz</span><span class="sxs-lookup"><span data-stu-id="08854-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="08854-760">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="08854-760">JSON Configuration Provider</span></span>

<span data-ttu-id="08854-761">Konfiguracja <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> ładowania z par klucza klucza json w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="08854-762">Aby aktywować konfigurację pliku <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> JSON, wywołaj metodę rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="08854-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="08854-763">Przeciążenia pozwalają na określenie:</span><span class="sxs-lookup"><span data-stu-id="08854-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="08854-764">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="08854-764">Whether the file is optional.</span></span>
* <span data-ttu-id="08854-765">Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.</span><span class="sxs-lookup"><span data-stu-id="08854-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="08854-766">Używany <xref:Microsoft.Extensions.FileProviders.IFileProvider> do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="08854-767">`AddJsonFile`jest automatycznie wywoływana dwa razy, gdy nowy `CreateDefaultBuilder`konstruktor hosta jest inicjowany za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="08854-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="08854-768">Metoda jest wywoływana do ładowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="08854-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="08854-769">*appsettings.json* &ndash; Ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="08854-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="08854-770">Wersja środowiska pliku może zastąpić wartości podane przez plik *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="08854-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="08854-771">*appsettings. {Środowisko}.json* &ndash; Wersja środowiska pliku jest ładowana na podstawie [pliku IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="08854-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="08854-772">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="08854-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="08854-773">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="08854-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="08854-774">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="08854-774">Environment variables.</span></span>
* <span data-ttu-id="08854-775">[Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="08854-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="08854-776">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="08854-776">Command-line arguments.</span></span>

<span data-ttu-id="08854-777">Dostawca konfiguracji JSON jest ustanawiany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="08854-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="08854-778">W związku z tym wpisy tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia zastępują konfigurację ustawioną przez pliki *zestawów aplikacji.*</span><span class="sxs-lookup"><span data-stu-id="08854-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="08854-779">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta w celu określenia konfiguracji aplikacji dla plików innych niż *appsettings.json* i *appsettings.{ Środowisko}.json*:</span><span class="sxs-lookup"><span data-stu-id="08854-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="08854-780">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="08854-780">**Example**</span></span>

<span data-ttu-id="08854-781">Przykładowa aplikacja korzysta z statycznej `CreateDefaultBuilder` metody wygody do tworzenia `AddJsonFile`hosta, która zawiera dwa wywołania:</span><span class="sxs-lookup"><span data-stu-id="08854-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="08854-782">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="08854-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="08854-783">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.{ Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="08854-784">Dla *appsettings. Development.json* w przykładowej aplikacji, załadowany jest następujący plik:</span><span class="sxs-lookup"><span data-stu-id="08854-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="08854-785">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="08854-785">Run the sample app.</span></span> <span data-ttu-id="08854-786">Otwórz przeglądarkę w `http://localhost:5000`aplikacji pod adresem .</span><span class="sxs-lookup"><span data-stu-id="08854-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="08854-787">Dane wyjściowe zawiera pary klucz-wartość dla konfiguracji na podstawie środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="08854-788">Poziom dziennika dla `Logging:LogLevel:Default` klucza jest `Debug` podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="08854-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="08854-789">Uruchom przykładową aplikację ponownie w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="08854-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="08854-790">Otwórz plik *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="08854-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="08854-791">W `ConfigurationSample` profilu zmień wartość zmiennej środowiskowej na `ASPNETCORE_ENVIRONMENT` `Production`.</span><span class="sxs-lookup"><span data-stu-id="08854-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="08854-792">Zapisz plik i uruchom `dotnet run` aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="08854-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="08854-793">Ustawienia w *appsettings. Development.json* nie zastępuje już ustawień w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="08854-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="08854-794">Poziom dziennika dla `Logging:LogLevel:Default` klucza to `Warning`.</span><span class="sxs-lookup"><span data-stu-id="08854-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="08854-795">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="08854-795">XML Configuration Provider</span></span>

<span data-ttu-id="08854-796">Konfiguracja <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> ładowania z par klucza klucza-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="08854-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="08854-797">Aby aktywować konfigurację pliku <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> XML, wywołaj metodę rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="08854-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="08854-798">Przeciążenia pozwalają na określenie:</span><span class="sxs-lookup"><span data-stu-id="08854-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="08854-799">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="08854-799">Whether the file is optional.</span></span>
* <span data-ttu-id="08854-800">Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.</span><span class="sxs-lookup"><span data-stu-id="08854-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="08854-801">Używany <xref:Microsoft.Extensions.FileProviders.IFileProvider> do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="08854-802">Węzeł główny pliku konfiguracyjnego jest ignorowany podczas tworzenia par klucza konfiguracji i wartości.</span><span class="sxs-lookup"><span data-stu-id="08854-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="08854-803">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="08854-804">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="08854-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="08854-805">Pliki konfiguracyjne XML mogą używać różnych nazw elementów dla sekcji powtarzanych:</span><span class="sxs-lookup"><span data-stu-id="08854-805">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="08854-806">Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`</span><span class="sxs-lookup"><span data-stu-id="08854-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="08854-807">section0:key0</span><span class="sxs-lookup"><span data-stu-id="08854-807">section0:key0</span></span>
* <span data-ttu-id="08854-808">section0:key1</span><span class="sxs-lookup"><span data-stu-id="08854-808">section0:key1</span></span>
* <span data-ttu-id="08854-809">sekcja1:key0</span><span class="sxs-lookup"><span data-stu-id="08854-809">section1:key0</span></span>
* <span data-ttu-id="08854-810">sekcja1:key1</span><span class="sxs-lookup"><span data-stu-id="08854-810">section1:key1</span></span>

<span data-ttu-id="08854-811">Powtarzające się elementy, które używają `name` tej samej nazwy elementu, działają, jeśli atrybut jest używany do rozróżniania elementów:</span><span class="sxs-lookup"><span data-stu-id="08854-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="08854-812">Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`</span><span class="sxs-lookup"><span data-stu-id="08854-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="08854-813">sekcja:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="08854-813">section:section0:key:key0</span></span>
* <span data-ttu-id="08854-814">sekcja:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="08854-814">section:section0:key:key1</span></span>
* <span data-ttu-id="08854-815">sekcja:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="08854-815">section:section1:key:key0</span></span>
* <span data-ttu-id="08854-816">sekcja:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="08854-816">section:section1:key:key1</span></span>

<span data-ttu-id="08854-817">Atrybuty mogą być używane do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="08854-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="08854-818">Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`</span><span class="sxs-lookup"><span data-stu-id="08854-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="08854-819">klucz:atrybut</span><span class="sxs-lookup"><span data-stu-id="08854-819">key:attribute</span></span>
* <span data-ttu-id="08854-820">sekcja:klucz:atrybut</span><span class="sxs-lookup"><span data-stu-id="08854-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="08854-821">Dostawca konfiguracji klucza na plik</span><span class="sxs-lookup"><span data-stu-id="08854-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="08854-822">Używa <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> plików katalogu jako par klucza konfiguracji wartości.</span><span class="sxs-lookup"><span data-stu-id="08854-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="08854-823">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-823">The key is the file name.</span></span> <span data-ttu-id="08854-824">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="08854-824">The value contains the file's contents.</span></span> <span data-ttu-id="08854-825">Dostawca konfiguracji klucza na plik jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="08854-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="08854-826">Aby aktywować konfigurację klucza <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> na plik, należy <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>wywołać metodę rozszerzenia w wystąpieniu programu .</span><span class="sxs-lookup"><span data-stu-id="08854-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="08854-827">Do `directoryPath` plików musi być ścieżka bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="08854-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="08854-828">Przeciążenia pozwalają na określenie:</span><span class="sxs-lookup"><span data-stu-id="08854-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="08854-829">Pełnomocnik, `Action<KeyPerFileConfigurationSource>` który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="08854-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="08854-830">Czy katalog jest opcjonalny i ścieżka do katalogu.</span><span class="sxs-lookup"><span data-stu-id="08854-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="08854-831">Podwójny znak podkreślenia (`__`) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="08854-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="08854-832">Na przykład nazwa `Logging__LogLevel__System` pliku tworzy klucz `Logging:LogLevel:System`konfiguracji .</span><span class="sxs-lookup"><span data-stu-id="08854-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="08854-833">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="08854-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="08854-834">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="08854-834">Memory Configuration Provider</span></span>

<span data-ttu-id="08854-835">Używa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekcji w pamięci jako pary klucz konfiguracji wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="08854-836">Aby aktywować konfigurację kolekcji <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> w pamięci, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>należy wywołać metodę rozszerzenia w wystąpieniu programu .</span><span class="sxs-lookup"><span data-stu-id="08854-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="08854-837">Dostawcę konfiguracji można zainicjować `IEnumerable<KeyValuePair<String,String>>`za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="08854-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="08854-838">Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="08854-839">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="08854-840">Słownik jest używany z wywołaniem, aby `AddInMemoryCollection` zapewnić konfigurację:</span><span class="sxs-lookup"><span data-stu-id="08854-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="08854-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="08854-841">GetValue</span></span>

<span data-ttu-id="08854-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji za pomocą określonego klucza i konwertuje ją na określony typ niezwiązanych z składem.</span><span class="sxs-lookup"><span data-stu-id="08854-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="08854-843">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="08854-843">An overload accepts a default value.</span></span>

<span data-ttu-id="08854-844">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="08854-844">The following example:</span></span>

* <span data-ttu-id="08854-845">Wyodrębnia wartość ciągu z konfiguracji `NumberKey`za pomocą klucza .</span><span class="sxs-lookup"><span data-stu-id="08854-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="08854-846">Jeśli `NumberKey` nie zostanie znaleziony w kluczach konfiguracyjnych, używana jest wartość domyślna. `99`</span><span class="sxs-lookup"><span data-stu-id="08854-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="08854-847">Wpisuje wartość `int`jako .</span><span class="sxs-lookup"><span data-stu-id="08854-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="08854-848">Przechowuje wartość `NumberConfig` we właściwości do użycia przez stronę.</span><span class="sxs-lookup"><span data-stu-id="08854-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="08854-849">GetSection, GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="08854-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="08854-850">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="08854-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="08854-851">Cztery klucze znajdują się w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="08854-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="08854-852">Po odczytaniu pliku do konfiguracji tworzone są następujące unikatowe klucze hierarchiczne w celu przechowywania wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="08854-853">section0:key0</span><span class="sxs-lookup"><span data-stu-id="08854-853">section0:key0</span></span>
* <span data-ttu-id="08854-854">section0:key1</span><span class="sxs-lookup"><span data-stu-id="08854-854">section0:key1</span></span>
* <span data-ttu-id="08854-855">sekcja1:key0</span><span class="sxs-lookup"><span data-stu-id="08854-855">section1:key0</span></span>
* <span data-ttu-id="08854-856">sekcja1:key1</span><span class="sxs-lookup"><span data-stu-id="08854-856">section1:key1</span></span>
* <span data-ttu-id="08854-857">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="08854-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="08854-858">section2:podsekcja0:key1</span><span class="sxs-lookup"><span data-stu-id="08854-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="08854-859">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="08854-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="08854-860">section2:podsekcja1:key1</span><span class="sxs-lookup"><span data-stu-id="08854-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="08854-861">Getsection</span><span class="sxs-lookup"><span data-stu-id="08854-861">GetSection</span></span>

<span data-ttu-id="08854-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="08854-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="08854-863">Aby zwrócić <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierającą tylko pary klucz-wartość `section1` `GetSection` w , wywołaj i podaj nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="08854-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="08854-864">Nie `configSection` ma wartości, tylko klucz i ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="08854-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="08854-865">Podobnie, aby uzyskać wartości dla `section2:subsection0`kluczy w , wywołaj `GetSection` i podaj ścieżkę przekroju:</span><span class="sxs-lookup"><span data-stu-id="08854-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="08854-866">`GetSection`nigdy `null`nie zwraca .</span><span class="sxs-lookup"><span data-stu-id="08854-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="08854-867">Jeśli pasująca sekcja nie zostanie `IConfigurationSection` znaleziona, zwracana jest pusta sekcja.</span><span class="sxs-lookup"><span data-stu-id="08854-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="08854-868">Po `GetSection` zwróceniu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> pasującej sekcji nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="08854-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="08854-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> i są zwracane, gdy sekcja istnieje.</span><span class="sxs-lookup"><span data-stu-id="08854-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="08854-870">Getchildren</span><span class="sxs-lookup"><span data-stu-id="08854-870">GetChildren</span></span>

<span data-ttu-id="08854-871">Wywołanie [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) `section2` na `IEnumerable<IConfigurationSection>` uzyskuje, który zawiera:</span><span class="sxs-lookup"><span data-stu-id="08854-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="08854-872">Exists</span><span class="sxs-lookup"><span data-stu-id="08854-872">Exists</span></span>

<span data-ttu-id="08854-873">Użyj [ConfigurationExtensions.Exists,](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) aby ustalić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="08854-874">Biorąc pod uwagę `sectionExists` `false` przykładowe dane, `section2:subsection2` jest to, ponieważ nie ma sekcji w danych konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="08854-875">Powiązanie wykresu obiektu</span><span class="sxs-lookup"><span data-stu-id="08854-875">Bind to an object graph</span></span>

<span data-ttu-id="08854-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>jest w stanie powiąże cały wykres obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="08854-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="08854-877">Podobnie jak w odniesieniu do wiązania prostego obiektu, tylko publiczne właściwości odczytu/zapisu są powiązane.</span><span class="sxs-lookup"><span data-stu-id="08854-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="08854-878">Przykład zawiera `TvShow` model, którego wykres `Metadata` `Actors` obiektów zawiera i klasy (*Modele/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="08854-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="08854-879">Przykładowa aplikacja ma plik *tvshow.xml* zawierający dane konfiguracyjne:</span><span class="sxs-lookup"><span data-stu-id="08854-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="08854-880">Konfiguracja jest powiązana z `TvShow` całym `Bind` wykresem obiektu za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="08854-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="08854-881">Powiązane wystąpienie jest przypisane do właściwości do renderowania:</span><span class="sxs-lookup"><span data-stu-id="08854-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="08854-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)wiąże i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="08854-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="08854-883">`Get<T>`jest wygodniejszy `Bind`niż korzystanie z programu .</span><span class="sxs-lookup"><span data-stu-id="08854-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="08854-884">Poniższy kod pokazuje, `Get<T>` jak używać z poprzednim przykładem:</span><span class="sxs-lookup"><span data-stu-id="08854-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="08854-885">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="08854-885">Bind an array to a class</span></span>

<span data-ttu-id="08854-886">*Przykładowa aplikacja demonstruje pojęcia wyjaśnione w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="08854-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="08854-887">Obsługuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> tablice powiązania do obiektów przy użyciu indeksów tablicy w kluczach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="08854-888">Każdy format tablicy, który udostępnia`:0:`segment `:1:` &hellip; `:{n}:`klucza numerycznego ( , , ) jest w stanie wiązania tablicy do tablicy klasy POCO.</span><span class="sxs-lookup"><span data-stu-id="08854-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="08854-889">Powiązanie jest przewidziane w konwencji.</span><span class="sxs-lookup"><span data-stu-id="08854-889">Binding is provided by convention.</span></span> <span data-ttu-id="08854-890">Dostawcy konfiguracji niestandardowej nie są wymagane do zaimplementowania powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="08854-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="08854-891">**Przetwarzanie macierzy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="08854-891">**In-memory array processing**</span></span>

<span data-ttu-id="08854-892">Należy wziąć pod uwagę klucze konfiguracji i wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="08854-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="08854-893">Klucz</span><span class="sxs-lookup"><span data-stu-id="08854-893">Key</span></span>             | <span data-ttu-id="08854-894">Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="08854-895">tablica:wpisy:0</span><span class="sxs-lookup"><span data-stu-id="08854-895">array:entries:0</span></span> | <span data-ttu-id="08854-896">wartość0</span><span class="sxs-lookup"><span data-stu-id="08854-896">value0</span></span> |
| <span data-ttu-id="08854-897">tablica:wpisy:1</span><span class="sxs-lookup"><span data-stu-id="08854-897">array:entries:1</span></span> | <span data-ttu-id="08854-898">wartość 1</span><span class="sxs-lookup"><span data-stu-id="08854-898">value1</span></span> |
| <span data-ttu-id="08854-899">tablica:wpisy:2</span><span class="sxs-lookup"><span data-stu-id="08854-899">array:entries:2</span></span> | <span data-ttu-id="08854-900">wartość2</span><span class="sxs-lookup"><span data-stu-id="08854-900">value2</span></span> |
| <span data-ttu-id="08854-901">tablica:wpisy:4</span><span class="sxs-lookup"><span data-stu-id="08854-901">array:entries:4</span></span> | <span data-ttu-id="08854-902">wartość4</span><span class="sxs-lookup"><span data-stu-id="08854-902">value4</span></span> |
| <span data-ttu-id="08854-903">tablica:wpisy:5</span><span class="sxs-lookup"><span data-stu-id="08854-903">array:entries:5</span></span> | <span data-ttu-id="08854-904">wartość5</span><span class="sxs-lookup"><span data-stu-id="08854-904">value5</span></span> |

<span data-ttu-id="08854-905">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="08854-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="08854-906">Tablica pomija wartość indeksu &num;3.</span><span class="sxs-lookup"><span data-stu-id="08854-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="08854-907">Spinacz konfiguracji nie jest w stanie powiązać wartości null lub tworzenia wpisów null w obiektach powiązanych, co staje się jasne w momencie, gdy wynik powiązania tej tablicy do obiektu jest pokazany.</span><span class="sxs-lookup"><span data-stu-id="08854-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="08854-908">W przykładowej aplikacji klasa POCO jest dostępna do przechowywania powiązanych danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="08854-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="08854-909">Dane konfiguracyjne są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="08854-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="08854-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)można również użyć składni, co skutkuje bardziej kompaktowym kodem:</span><span class="sxs-lookup"><span data-stu-id="08854-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="08854-911">Obiekt powiązany, wystąpienie `ArrayExample`, odbiera dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="08854-912">`ArrayExample.Entries`Indeks</span><span class="sxs-lookup"><span data-stu-id="08854-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="08854-913">`ArrayExample.Entries`Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="08854-914">0</span><span class="sxs-lookup"><span data-stu-id="08854-914">0</span></span>                            | <span data-ttu-id="08854-915">wartość0</span><span class="sxs-lookup"><span data-stu-id="08854-915">value0</span></span>                       |
| <span data-ttu-id="08854-916">1</span><span class="sxs-lookup"><span data-stu-id="08854-916">1</span></span>                            | <span data-ttu-id="08854-917">wartość 1</span><span class="sxs-lookup"><span data-stu-id="08854-917">value1</span></span>                       |
| <span data-ttu-id="08854-918">2</span><span class="sxs-lookup"><span data-stu-id="08854-918">2</span></span>                            | <span data-ttu-id="08854-919">wartość2</span><span class="sxs-lookup"><span data-stu-id="08854-919">value2</span></span>                       |
| <span data-ttu-id="08854-920">3</span><span class="sxs-lookup"><span data-stu-id="08854-920">3</span></span>                            | <span data-ttu-id="08854-921">wartość4</span><span class="sxs-lookup"><span data-stu-id="08854-921">value4</span></span>                       |
| <span data-ttu-id="08854-922">4</span><span class="sxs-lookup"><span data-stu-id="08854-922">4</span></span>                            | <span data-ttu-id="08854-923">wartość5</span><span class="sxs-lookup"><span data-stu-id="08854-923">value5</span></span>                       |

<span data-ttu-id="08854-924">Indeks &num;3 w obiekcie powiązanym `array:4` przechowuje dane konfiguracyjne klucza konfiguracji i jego wartość `value4`.</span><span class="sxs-lookup"><span data-stu-id="08854-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="08854-925">Gdy dane konfiguracyjne zawierające tablicę są powiązane, indeksy tablicowe w kluczach konfiguracji są używane jedynie do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="08854-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="08854-926">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracyjnych pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="08854-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="08854-927">Brakujący element konfiguracji &num;dla indeksu 3 `ArrayExample` może być dostarczony przed powiązaniem z wystąpieniem przez dowolnego dostawcę konfiguracji, który tworzy poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="08854-928">Jeśli przykład zawierał dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, jest ona `ArrayExample.Entries` zgodna z pełną tablicą konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="08854-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="08854-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="08854-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="08854-930">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="08854-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="08854-931">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="08854-932">Klucz</span><span class="sxs-lookup"><span data-stu-id="08854-932">Key</span></span>             | <span data-ttu-id="08854-933">Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="08854-934">tablica:wpisy:3</span><span class="sxs-lookup"><span data-stu-id="08854-934">array:entries:3</span></span> | <span data-ttu-id="08854-935">wartość3</span><span class="sxs-lookup"><span data-stu-id="08854-935">value3</span></span> |

<span data-ttu-id="08854-936">Jeśli `ArrayExample` wystąpienie klasy jest powiązane po dostawcy konfiguracji &num;JSON zawiera `ArrayExample.Entries` wpis dla indeksu 3, tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="08854-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="08854-937">`ArrayExample.Entries`Indeks</span><span class="sxs-lookup"><span data-stu-id="08854-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="08854-938">`ArrayExample.Entries`Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="08854-939">0</span><span class="sxs-lookup"><span data-stu-id="08854-939">0</span></span>                            | <span data-ttu-id="08854-940">wartość0</span><span class="sxs-lookup"><span data-stu-id="08854-940">value0</span></span>                       |
| <span data-ttu-id="08854-941">1</span><span class="sxs-lookup"><span data-stu-id="08854-941">1</span></span>                            | <span data-ttu-id="08854-942">wartość 1</span><span class="sxs-lookup"><span data-stu-id="08854-942">value1</span></span>                       |
| <span data-ttu-id="08854-943">2</span><span class="sxs-lookup"><span data-stu-id="08854-943">2</span></span>                            | <span data-ttu-id="08854-944">wartość2</span><span class="sxs-lookup"><span data-stu-id="08854-944">value2</span></span>                       |
| <span data-ttu-id="08854-945">3</span><span class="sxs-lookup"><span data-stu-id="08854-945">3</span></span>                            | <span data-ttu-id="08854-946">wartość3</span><span class="sxs-lookup"><span data-stu-id="08854-946">value3</span></span>                       |
| <span data-ttu-id="08854-947">4</span><span class="sxs-lookup"><span data-stu-id="08854-947">4</span></span>                            | <span data-ttu-id="08854-948">wartość4</span><span class="sxs-lookup"><span data-stu-id="08854-948">value4</span></span>                       |
| <span data-ttu-id="08854-949">5</span><span class="sxs-lookup"><span data-stu-id="08854-949">5</span></span>                            | <span data-ttu-id="08854-950">wartość5</span><span class="sxs-lookup"><span data-stu-id="08854-950">value5</span></span>                       |

<span data-ttu-id="08854-951">**Przetwarzanie macierzy JSON**</span><span class="sxs-lookup"><span data-stu-id="08854-951">**JSON array processing**</span></span>

<span data-ttu-id="08854-952">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji opartej na wartości zerowej.</span><span class="sxs-lookup"><span data-stu-id="08854-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="08854-953">W następującym pliku `subsection` konfiguracyjnym jest tablica:</span><span class="sxs-lookup"><span data-stu-id="08854-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="08854-954">Dostawca konfiguracji JSON odczytuje dane konfiguracyjne do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="08854-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="08854-955">Klucz</span><span class="sxs-lookup"><span data-stu-id="08854-955">Key</span></span>                     | <span data-ttu-id="08854-956">Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="08854-957">json_array:key</span><span class="sxs-lookup"><span data-stu-id="08854-957">json_array:key</span></span>          | <span data-ttu-id="08854-958">wartośćAA</span><span class="sxs-lookup"><span data-stu-id="08854-958">valueA</span></span> |
| <span data-ttu-id="08854-959">json_array:podsekcja:0</span><span class="sxs-lookup"><span data-stu-id="08854-959">json_array:subsection:0</span></span> | <span data-ttu-id="08854-960">wartośćB</span><span class="sxs-lookup"><span data-stu-id="08854-960">valueB</span></span> |
| <span data-ttu-id="08854-961">json_array:podsekcja:1</span><span class="sxs-lookup"><span data-stu-id="08854-961">json_array:subsection:1</span></span> | <span data-ttu-id="08854-962">wartośćC</span><span class="sxs-lookup"><span data-stu-id="08854-962">valueC</span></span> |
| <span data-ttu-id="08854-963">json_array:podsekcja:2</span><span class="sxs-lookup"><span data-stu-id="08854-963">json_array:subsection:2</span></span> | <span data-ttu-id="08854-964">Wyceniane</span><span class="sxs-lookup"><span data-stu-id="08854-964">valueD</span></span> |

<span data-ttu-id="08854-965">W przykładowej aplikacji dostępna jest następująca klasa POCO do powiązania par klucza konfiguracji z wartością:</span><span class="sxs-lookup"><span data-stu-id="08854-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="08854-966">Po `JsonArrayExample.Key` wiązaniu, `valueA`posiada wartość .</span><span class="sxs-lookup"><span data-stu-id="08854-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="08854-967">Wartości podsekcji są przechowywane we właściwości `Subsection`tablicy POCO, .</span><span class="sxs-lookup"><span data-stu-id="08854-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="08854-968">`JsonArrayExample.Subsection`Indeks</span><span class="sxs-lookup"><span data-stu-id="08854-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="08854-969">`JsonArrayExample.Subsection`Wartość</span><span class="sxs-lookup"><span data-stu-id="08854-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="08854-970">0</span><span class="sxs-lookup"><span data-stu-id="08854-970">0</span></span>                                   | <span data-ttu-id="08854-971">wartośćB</span><span class="sxs-lookup"><span data-stu-id="08854-971">valueB</span></span>                              |
| <span data-ttu-id="08854-972">1</span><span class="sxs-lookup"><span data-stu-id="08854-972">1</span></span>                                   | <span data-ttu-id="08854-973">wartośćC</span><span class="sxs-lookup"><span data-stu-id="08854-973">valueC</span></span>                              |
| <span data-ttu-id="08854-974">2</span><span class="sxs-lookup"><span data-stu-id="08854-974">2</span></span>                                   | <span data-ttu-id="08854-975">Wyceniane</span><span class="sxs-lookup"><span data-stu-id="08854-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="08854-976">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="08854-976">Custom configuration provider</span></span>

<span data-ttu-id="08854-977">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucza konfiguracji z bazy danych przy użyciu [entity framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="08854-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="08854-978">Dostawca posiada następujące cechy:</span><span class="sxs-lookup"><span data-stu-id="08854-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="08854-979">Baza danych EF w pamięci jest używana do celów demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="08854-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="08854-980">Aby użyć bazy danych, która wymaga `ConfigurationBuilder` ciągu połączenia, należy zaimplementować pomocniczy do poniesienia ciągu połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="08854-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="08854-981">Dostawca odczytuje tabelę bazy danych do konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="08854-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="08854-982">Dostawca nie wysyła zapytania do bazy danych na podstawie klucza.</span><span class="sxs-lookup"><span data-stu-id="08854-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="08854-983">Ponowne ładowanie na zmianę nie jest zaimplementowana, więc aktualizowanie bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="08854-984">Zdefiniuj encję `EFConfigurationValue` do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="08854-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="08854-985">*Modele/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="08854-986">Dodaj `EFConfigurationContext` do przechowywania i dostępu do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="08854-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="08854-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="08854-988">Utwórz klasę implementuują . <xref:Microsoft.Extensions.Configuration.IConfigurationSource></span><span class="sxs-lookup"><span data-stu-id="08854-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="08854-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="08854-990">Utwórz niestandardowego dostawcę <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>konfiguracji, dziedzicząc po programie .</span><span class="sxs-lookup"><span data-stu-id="08854-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="08854-991">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="08854-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="08854-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="08854-993">Metoda `AddEFConfiguration` rozszerzenia umożliwia dodanie źródła `ConfigurationBuilder`konfiguracji do pliku .</span><span class="sxs-lookup"><span data-stu-id="08854-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="08854-994">*Rozszerzenia/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="08854-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="08854-995">Poniższy kod pokazuje, jak `EFConfigurationProvider` używać niestandardowych w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="08854-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="08854-996">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="08854-996">Access configuration during startup</span></span>

<span data-ttu-id="08854-997">Wstrzyknąć `IConfiguration` do konstruktora, `Startup` aby uzyskać dostęp do wartości konfiguracji w `Startup.ConfigureServices`programie .</span><span class="sxs-lookup"><span data-stu-id="08854-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="08854-998">Aby uzyskać `Startup.Configure`dostęp do `IConfiguration` konfiguracji w , albo wstrzyknąć bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="08854-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="08854-999">Na przykład uzyskiwania dostępu do konfiguracji przy użyciu metod wygody uruchamiania, zobacz [Uruchamianie aplikacji: Metody wygody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="08854-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="08854-1000">Uzyskiwanie dostępu do konfiguracji na stronie Razor Pages lub w widoku MVC</span><span class="sxs-lookup"><span data-stu-id="08854-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="08854-1001">Aby uzyskać dostęp do ustawień konfiguracji na stronie Strony Razor lub widoku MVC, dodaj [using dyrektywy](xref:mvc/views/razor#using) [(Odwołanie C#: using directive)](/dotnet/csharp/language-reference/keywords/using-directive)dla [obszaru nazw Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="08854-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="08854-1002">Na stronie Strony Maszynki do Golenia:</span><span class="sxs-lookup"><span data-stu-id="08854-1002">In a Razor Pages page:</span></span>

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

<span data-ttu-id="08854-1003">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="08854-1003">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="08854-1004">Dodawanie konfiguracji z zespołu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="08854-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="08854-1005">Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania `Startup` z zewnętrznego zestawu poza klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08854-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="08854-1006">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="08854-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08854-1007">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="08854-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
