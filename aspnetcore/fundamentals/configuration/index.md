---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core przy użyciu interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c6b77f20792c32a697fff8d1d78ef3c2536c5f8f
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722900"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="16079-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16079-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="16079-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="16079-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="16079-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="16079-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="16079-106">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="16079-107">Pliki ustawień, takie jak *appsettings.jswłączone*</span><span class="sxs-lookup"><span data-stu-id="16079-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="16079-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="16079-108">Environment variables</span></span>
* <span data-ttu-id="16079-109">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16079-109">Azure Key Vault</span></span>
* <span data-ttu-id="16079-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="16079-110">Azure App Configuration</span></span>
* <span data-ttu-id="16079-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-111">Command-line arguments</span></span>
* <span data-ttu-id="16079-112">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="16079-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="16079-113">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="16079-113">Directory files</span></span>
* <span data-ttu-id="16079-114">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="16079-114">In-memory .NET objects</span></span>

<span data-ttu-id="16079-115">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16079-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="16079-116">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="16079-116">Default configuration</span></span>

<span data-ttu-id="16079-117">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="16079-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="16079-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="16079-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="16079-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący element `IConfiguration` jako źródło.</span><span class="sxs-lookup"><span data-stu-id="16079-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="16079-120">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="16079-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="16079-121">[appsettings.js](#appsettingsjson) przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="16079-122">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="16079-123">Na przykład *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. *kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="16079-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="16079-124">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="16079-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="16079-125">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="16079-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="16079-126">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="16079-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="16079-127">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="16079-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="16079-128">Na przykład jeśli `MyKey` jest ustawiona zarówno w *appsettings.js* , jak i w środowisku, zostanie użyta wartość środowiska.</span><span class="sxs-lookup"><span data-stu-id="16079-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="16079-129">Przy użyciu domyślnych dostawców konfiguracji  [dostawca konfiguracji wiersza polecenia](#clcp) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="16079-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="16079-130">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="16079-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="16079-131">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="16079-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="16079-132">appsettings.jsna</span><span class="sxs-lookup"><span data-stu-id="16079-132">appsettings.json</span></span>

<span data-ttu-id="16079-133">Rozważmy następujące *appsettings.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="16079-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="16079-134">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-135">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="16079-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="16079-136">*appsettings.jsna*</span><span class="sxs-lookup"><span data-stu-id="16079-136">*appsettings.json*</span></span>
1. <span data-ttu-id="16079-137">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="16079-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="16079-138">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="16079-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="16079-139">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="16079-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="16079-140">*AppSettings*. `Environment` . wartości *JSON* przesłaniają klucze w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="16079-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="16079-141">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="16079-141">For example, by default:</span></span>

* <span data-ttu-id="16079-142">W obszarze programowanie, *AppSettings*. ***Programowanie***. Konfiguracja *JSON* zastępuje wartości znalezione w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="16079-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="16079-143">W obszarze produkcja, *AppSettings*. ***Środowisko produkcyjne***. Konfiguracja *JSON* zastępuje wartości znalezione w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="16079-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="16079-144">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="16079-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="16079-145">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="16079-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="16079-146">Korzystając z konfiguracji [domyślnej](#default) , *appsettings.jsna* i *appSettings.* `Environment` pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="16079-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="16079-147">Zmiany wprowadzone w *appsettings.js* i *appSettings.* `Environment` plik *. JSON* ***po*** uruchomieniu aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="16079-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="16079-148">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="16079-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="16079-149">Łączenie kolekcji usług</span><span class="sxs-lookup"><span data-stu-id="16079-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="16079-150">Security and Secret Manager</span><span class="sxs-lookup"><span data-stu-id="16079-150">Security and secret manager</span></span>

<span data-ttu-id="16079-151">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="16079-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="16079-152">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="16079-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="16079-153">Za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="16079-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="16079-154">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="16079-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="16079-155">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="16079-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="16079-156">[Domyślnie](#default)program [Secret Manager](xref:security/app-secrets) odczytuje ustawienia konfiguracji po *appsettings.jsna* i *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="16079-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="16079-157">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="16079-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="16079-158"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="16079-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="16079-159">Menedżer wpisów tajnych używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="16079-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="16079-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="16079-161">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="16079-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="16079-162">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="16079-162">Environment variables</span></span>

<span data-ttu-id="16079-163">Przy użyciu konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość po czytaniu *appsettings.jsw*, *appSettings.* `Environment` *. JSON*i [Menedżer wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="16079-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="16079-164">W związku z tym kluczowe wartości są odczytywane z wartości zastąpienia środowiska odczytywane z *appsettings.jsw*, *appSettings.* `Environment` *. JSON*i Menedżer wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="16079-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="16079-165">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="16079-165">The following `set` commands:</span></span>

* <span data-ttu-id="16079-166">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="16079-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="16079-167">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="16079-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="16079-168">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="16079-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="16079-169">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="16079-169">The preceding environment settings:</span></span>

* <span data-ttu-id="16079-170">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="16079-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="16079-171">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="16079-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="16079-172">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="16079-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="16079-173">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="16079-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="16079-174">`/M` ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="16079-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="16079-175">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="16079-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="16079-176">Aby sprawdzić, czy poprzednie polecenia zastępują *appsettings.js* i *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="16079-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="16079-177">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="16079-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="16079-178">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i wprowadź `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="16079-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="16079-179">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="16079-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="16079-180">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="16079-180">In the preceding code:</span></span>

* <span data-ttu-id="16079-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="16079-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="16079-182">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="16079-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="16079-183">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="16079-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="16079-184">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="16079-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="16079-185">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="16079-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="16079-186">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="16079-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="16079-187">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="16079-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="16079-188">`DOTNET_` `ASPNETCORE_` Prefiksy i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="16079-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="16079-189">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="16079-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="16079-190">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="16079-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="16079-191">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="16079-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="16079-192">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="16079-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="16079-193">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16079-193">Exposed as environment variables.</span></span>

<span data-ttu-id="16079-194">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="16079-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="16079-195">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="16079-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="16079-196">Zmienne środowiskowe ustawione w launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="16079-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="16079-197">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="16079-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="16079-198">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-198">Command-line</span></span>

<span data-ttu-id="16079-199">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="16079-200">*appsettings.jsna* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="16079-200">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="16079-201">Wpisy [tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="16079-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="16079-202">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16079-202">Environment variables.</span></span>

<span data-ttu-id="16079-203">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="16079-204">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-204">Command-line arguments</span></span>

<span data-ttu-id="16079-205">Następujące polecenie ustawia klucze i wartości przy użyciu `=` :</span><span class="sxs-lookup"><span data-stu-id="16079-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="16079-206">Następujące polecenie ustawia klucze i wartości przy użyciu `/` :</span><span class="sxs-lookup"><span data-stu-id="16079-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="16079-207">Następujące polecenie ustawia klucze i wartości przy użyciu `--` :</span><span class="sxs-lookup"><span data-stu-id="16079-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="16079-208">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="16079-208">The key value:</span></span>

* <span data-ttu-id="16079-209">Musi `=` być zgodna lub musi mieć prefiks lub, gdy wartość znajduje się w `--` `/` miejscu.</span><span class="sxs-lookup"><span data-stu-id="16079-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="16079-210">Nie jest wymagane, jeśli `=` jest używany.</span><span class="sxs-lookup"><span data-stu-id="16079-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="16079-211">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="16079-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="16079-212">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które są używane `=` z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="16079-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="16079-213">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="16079-213">Switch mappings</span></span>

<span data-ttu-id="16079-214">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="16079-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="16079-215">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="16079-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="16079-216">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="16079-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="16079-217">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="16079-218">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="16079-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="16079-219">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="16079-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="16079-220">Przełączniki muszą zaczynać się od `-` lub `--` .</span><span class="sxs-lookup"><span data-stu-id="16079-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="16079-221">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="16079-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="16079-222">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="16079-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="16079-223">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="16079-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-224">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="16079-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="16079-225">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="16079-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="16079-226">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="16079-227">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="16079-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="16079-228">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-228">Hierarchical configuration data</span></span>

<span data-ttu-id="16079-229">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="16079-230">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="16079-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="16079-231">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-232">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="16079-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="16079-233">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="16079-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="16079-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="16079-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="16079-235">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="16079-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="16079-236">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-236">Configuration keys and values</span></span>

<span data-ttu-id="16079-237">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-237">Configuration keys:</span></span>

* <span data-ttu-id="16079-238">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="16079-238">Are case-insensitive.</span></span> <span data-ttu-id="16079-239">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="16079-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="16079-240">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="16079-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="16079-241">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="16079-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="16079-242">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="16079-242">Hierarchical keys</span></span>
  * <span data-ttu-id="16079-243">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="16079-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="16079-244">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="16079-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="16079-245">Podwójne podkreślenie, `__` ,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:` .</span><span class="sxs-lookup"><span data-stu-id="16079-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="16079-246">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="16079-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="16079-247">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="16079-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="16079-249">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="16079-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="16079-250">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-250">Configuration values:</span></span>

* <span data-ttu-id="16079-251">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="16079-251">Are strings.</span></span>
* <span data-ttu-id="16079-252">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="16079-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="16079-253">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-253">Configuration providers</span></span>

<span data-ttu-id="16079-254">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="16079-255">Dostawca</span><span class="sxs-lookup"><span data-stu-id="16079-255">Provider</span></span> | <span data-ttu-id="16079-256">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="16079-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="16079-257">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16079-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="16079-258">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16079-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="16079-259">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="16079-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="16079-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="16079-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="16079-261">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="16079-262">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-262">Command-line parameters</span></span> |
| [<span data-ttu-id="16079-263">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="16079-264">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="16079-264">Custom source</span></span> |
| [<span data-ttu-id="16079-265">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="16079-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="16079-266">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="16079-266">Environment variables</span></span> |
| [<span data-ttu-id="16079-267">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="16079-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="16079-268">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="16079-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="16079-269">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="16079-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="16079-270">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="16079-270">Directory files</span></span> |
| [<span data-ttu-id="16079-271">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="16079-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="16079-272">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="16079-272">In-memory collections</span></span> |
| [<span data-ttu-id="16079-273">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="16079-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="16079-274">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="16079-274">File in the user profile directory</span></span> |

<span data-ttu-id="16079-275">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="16079-276">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="16079-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="16079-277">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="16079-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="16079-278">*appsettings.jsna*</span><span class="sxs-lookup"><span data-stu-id="16079-278">*appsettings.json*</span></span>
1. <span data-ttu-id="16079-279">*AppSettings*. `Environment` . *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="16079-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="16079-280">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="16079-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="16079-281">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="16079-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="16079-282">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="16079-283">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="16079-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="16079-284">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="16079-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="16079-285">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="16079-285">Connection string prefixes</span></span>

<span data-ttu-id="16079-286">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="16079-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="16079-287">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="16079-288">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="16079-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="16079-289">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="16079-289">Connection string prefix</span></span> | <span data-ttu-id="16079-290">Dostawca</span><span class="sxs-lookup"><span data-stu-id="16079-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="16079-291">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="16079-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="16079-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="16079-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="16079-293">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="16079-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="16079-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="16079-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="16079-295">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="16079-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="16079-296">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="16079-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="16079-297">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="16079-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="16079-298">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="16079-298">Environment variable key</span></span> | <span data-ttu-id="16079-299">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-299">Converted configuration key</span></span> | <span data-ttu-id="16079-300">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="16079-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-301">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="16079-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-302">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="16079-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="16079-303">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="16079-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-304">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="16079-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="16079-305">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="16079-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-306">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="16079-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="16079-307">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="16079-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="16079-308">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="16079-308">File configuration provider</span></span>

<span data-ttu-id="16079-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="16079-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="16079-310">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="16079-310">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="16079-311">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="16079-311">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="16079-312">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="16079-312">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="16079-313">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="16079-313">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="16079-314">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="16079-314">INI configuration provider</span></span>

<span data-ttu-id="16079-315"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="16079-315">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="16079-316">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-316">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="16079-317">W powyższym kodzie ustawienia w *MyIniConfig.ini* i  *MyIniConfig*. `Environment` . pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="16079-317">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="16079-318">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="16079-318">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="16079-319">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="16079-319">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="16079-320">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="16079-320">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="16079-321">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-321">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="16079-322">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="16079-322">JSON configuration provider</span></span>

<span data-ttu-id="16079-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="16079-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="16079-324">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="16079-324">Overloads can specify:</span></span>

* <span data-ttu-id="16079-325">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="16079-325">Whether the file is optional.</span></span>
* <span data-ttu-id="16079-326">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="16079-327">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="16079-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="16079-328">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="16079-328">The preceding code:</span></span>

* <span data-ttu-id="16079-329">Konfiguruje dostawcę konfiguracji JSON w celu załadowania *MyConfig.jsw* pliku z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="16079-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="16079-330">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="16079-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="16079-331">`reloadOnChange: true` : Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="16079-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="16079-332">Odczytuje [domyślnych dostawców konfiguracji](#default) przed *MyConfig.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="16079-333">Ustawienia w *MyConfig.js* ustawienia przesłania pliku w domyślnych dostawcach konfiguracji, w tym [dostawca konfiguracji zmiennych środowiskowych](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="16079-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="16079-334">Zwykle ***nie*** chcesz, aby niestandardowy plik JSON zastępujący wartości ustawione w [zmiennej środowiskowej dostawcy konfiguracji](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="16079-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="16079-335">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="16079-336">W powyższym kodzie ustawienia w *MyConfig.jsna* i  *konfiguracji*. `Environment` .. pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="16079-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="16079-337">Zastąp ustawienia w *appsettings.js* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="16079-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="16079-338">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="16079-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="16079-339">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *MyConfig.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="16079-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="16079-340">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="16079-341">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="16079-341">XML configuration provider</span></span>

<span data-ttu-id="16079-342"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="16079-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="16079-343">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="16079-344">W powyższym kodzie ustawienia w *MyXMLFile.xml* i  *MyXMLFile*. `Environment` . pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="16079-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="16079-345">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="16079-345">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="16079-346">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="16079-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="16079-347">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="16079-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="16079-348">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-349">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="16079-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="16079-350">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="16079-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-351">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="16079-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="16079-352">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="16079-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="16079-353">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="16079-353">key:attribute</span></span>
* <span data-ttu-id="16079-354">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="16079-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="16079-355">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="16079-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="16079-356"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="16079-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="16079-357">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-357">The key is the file name.</span></span> <span data-ttu-id="16079-358">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-358">The value contains the file's contents.</span></span> <span data-ttu-id="16079-359">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="16079-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="16079-360">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="16079-361">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="16079-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="16079-362">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="16079-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="16079-363">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="16079-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="16079-364">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="16079-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="16079-365">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="16079-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="16079-366">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="16079-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="16079-367">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="16079-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="16079-368">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="16079-368">Memory configuration provider</span></span>

<span data-ttu-id="16079-369"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="16079-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="16079-370">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="16079-371">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-372">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="16079-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="16079-373">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="16079-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="16079-374">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="16079-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="16079-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="16079-375">GetValue</span></span>

<span data-ttu-id="16079-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="16079-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-377">W powyższym kodzie, jeśli `NumberKey` nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="16079-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="16079-378">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="16079-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="16079-379">W poniższych przykładach należy wziąć pod uwagę następujące *MySubsection.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="16079-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="16079-380">Poniższy kod dodaje *MySubsection.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="16079-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="16079-381">GetSection</span></span>

<span data-ttu-id="16079-382">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="16079-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="16079-383">Poniższy kod zwraca wartości dla `section1` :</span><span class="sxs-lookup"><span data-stu-id="16079-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-384">Poniższy kod zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="16079-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-385">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="16079-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="16079-386">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="16079-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="16079-387">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="16079-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="16079-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="16079-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="16079-389">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="16079-389">GetChildren and Exists</span></span>

<span data-ttu-id="16079-390">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="16079-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-391">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="16079-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="16079-392">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="16079-392">Bind an array</span></span>

<span data-ttu-id="16079-393">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="16079-394">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="16079-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="16079-395">Rozważ *MyArray.jsna* podstawie pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="16079-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="16079-396">Poniższy kod dodaje *MyArray.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="16079-397">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="16079-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-398">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="16079-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="16079-399">W poprzednich danych wyjściowych indeks 3 ma wartość `value40` odpowiadającą wartości `"4": "value40",` w *MyArray.jsna*.</span><span class="sxs-lookup"><span data-stu-id="16079-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="16079-400">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="16079-401">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="16079-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="16079-402">Poniższy kod ładuje `array:entries` konfigurację z użyciem <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="16079-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="16079-403">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="16079-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-404">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="16079-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="16079-405">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="16079-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="16079-406">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="16079-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="16079-407">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="16079-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="16079-408">Brakujący element konfiguracji dla indeksu &num; 3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje &num; parę klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="16079-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="16079-409">Rozważmy następujące *Value3.js* pliku z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="16079-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="16079-410">Poniższy kod zawiera konfigurację dla *Value3.js* i `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="16079-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="16079-411">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="16079-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-412">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="16079-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="16079-413">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="16079-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="16079-414">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-414">Custom configuration provider</span></span>

<span data-ttu-id="16079-415">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="16079-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="16079-416">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="16079-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="16079-417">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="16079-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="16079-418">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="16079-419">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="16079-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="16079-420">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="16079-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="16079-421">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="16079-422">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="16079-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="16079-423">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="16079-424">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="16079-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="16079-425">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="16079-426">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="16079-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="16079-427">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="16079-428">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="16079-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="16079-429">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="16079-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="16079-430">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="16079-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="16079-431">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="16079-432">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="16079-433">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="16079-434">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="16079-435">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="16079-435">Access configuration in Startup</span></span>

<span data-ttu-id="16079-436">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="16079-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="16079-437">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="16079-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="16079-438">Konfiguracja dostępu na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="16079-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="16079-439">Poniższy kod przedstawia dane konfiguracji na Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="16079-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="16079-440">W poniższym kodzie `MyOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="16079-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="16079-441">Następujące oznakowanie używa [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy do rozwiązywania i wyświetlania wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="16079-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="16079-442">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="16079-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="16079-443">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="16079-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="16079-444">Konfigurowanie opcji za pomocą delegata</span><span class="sxs-lookup"><span data-stu-id="16079-444">Configure options with a delegate</span></span>

<span data-ttu-id="16079-445">Opcje skonfigurowane w wartościach zastąpień delegatów ustawionych w dostawcach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="16079-446">Konfigurowanie opcji za pomocą delegata jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="16079-447">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="16079-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="16079-448">Używa delegata do konfigurowania wartości dla `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="16079-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="16079-449">Poniższy kod wyświetla wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="16079-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="16079-450">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w *appsettings.jsna* , a następnie zastąpione przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="16079-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="16079-451">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="16079-451">Host versus app configuration</span></span>

<span data-ttu-id="16079-452">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="16079-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="16079-453">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="16079-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="16079-454">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="16079-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="16079-455">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="16079-456">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="16079-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="16079-457">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="16079-457">Default host configuration</span></span>

<span data-ttu-id="16079-458">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="16079-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="16079-459">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="16079-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="16079-460">Zmienne środowiskowe poprzedzone znakiem `DOTNET_` (na przykład `DOTNET_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="16079-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="16079-461">Prefiks ( `DOTNET_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="16079-462">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="16079-463">Konfiguracja domyślna hosta sieci Web ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="16079-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="16079-464">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="16079-465">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="16079-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="16079-466">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` zmienna środowiskowa jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="16079-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="16079-467">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="16079-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="16079-468">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="16079-468">Other configuration</span></span>

<span data-ttu-id="16079-469">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="16079-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="16079-470">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="16079-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="16079-471">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="16079-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="16079-472">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="16079-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="16079-473">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="16079-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="16079-474">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="16079-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="16079-475">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="16079-475">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="16079-476">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="16079-476">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="16079-477">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="16079-477">Add configuration from an external assembly</span></span>

<span data-ttu-id="16079-478"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-478">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="16079-479">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="16079-479">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16079-480">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="16079-480">Additional resources</span></span>

* [<span data-ttu-id="16079-481">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-481">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="16079-482">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji*.</span><span class="sxs-lookup"><span data-stu-id="16079-482">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="16079-483">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-483">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="16079-484">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16079-484">Azure Key Vault</span></span>
* <span data-ttu-id="16079-485">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="16079-485">Azure App Configuration</span></span>
* <span data-ttu-id="16079-486">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-486">Command-line arguments</span></span>
* <span data-ttu-id="16079-487">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="16079-487">Custom providers (installed or created)</span></span>
* <span data-ttu-id="16079-488">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="16079-488">Directory files</span></span>
* <span data-ttu-id="16079-489">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="16079-489">Environment variables</span></span>
* <span data-ttu-id="16079-490">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="16079-490">In-memory .NET objects</span></span>
* <span data-ttu-id="16079-491">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="16079-491">Settings files</span></span>

<span data-ttu-id="16079-492">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="16079-492">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="16079-493">Przykłady kodu, które obserwują i w przykładowej aplikacji używają <xref:Microsoft.Extensions.Configuration> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="16079-493">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="16079-494">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="16079-494">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="16079-495">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="16079-495">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="16079-496">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="16079-496">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="16079-497">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16079-497">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="16079-498">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="16079-498">Host versus app configuration</span></span>

<span data-ttu-id="16079-499">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="16079-499">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="16079-500">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="16079-500">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="16079-501">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="16079-501">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="16079-502">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-502">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="16079-503">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="16079-503">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="16079-504">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="16079-504">Other configuration</span></span>

<span data-ttu-id="16079-505">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="16079-505">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="16079-506">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="16079-506">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="16079-507">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="16079-507">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="16079-508">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="16079-508">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="16079-509">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="16079-509">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="16079-510">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="16079-510">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="16079-511">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="16079-511">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="16079-512">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="16079-512">Default configuration</span></span>

<span data-ttu-id="16079-513">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych szablonów dotnet](/dotnet/core/tools/dotnet-new) są wywoływane <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="16079-513">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="16079-514">`CreateDefaultBuilder` zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="16079-514">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="16079-515">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="16079-515">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="16079-516">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="16079-516">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="16079-517">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="16079-517">Host configuration is provided from:</span></span>
  * <span data-ttu-id="16079-518">Zmienne środowiskowe poprzedzone znakiem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-518">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="16079-519">Prefiks ( `ASPNETCORE_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-519">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="16079-520">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-520">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="16079-521">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="16079-521">App configuration is provided from:</span></span>
  * <span data-ttu-id="16079-522">*appsettings.js* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-522">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="16079-523">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-523">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="16079-524">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="16079-524">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="16079-525">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-525">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="16079-526">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="16079-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="16079-527">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="16079-527">Security</span></span>

<span data-ttu-id="16079-528">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="16079-528">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="16079-529">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="16079-529">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="16079-530">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="16079-530">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="16079-531">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="16079-531">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="16079-532">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="16079-532">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="16079-533"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="16079-533"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="16079-534">Menedżer wpisów tajnych używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="16079-534">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="16079-535">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="16079-535">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="16079-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="16079-537">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="16079-537">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="16079-538">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-538">Hierarchical configuration data</span></span>

<span data-ttu-id="16079-539">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-539">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="16079-540">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="16079-540">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="16079-541">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-541">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="16079-542">Sekcje i klucze są spłaszczone przy użyciu dwukropka (), `:` Aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="16079-542">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="16079-543">section0:key0</span><span class="sxs-lookup"><span data-stu-id="16079-543">section0:key0</span></span>
* <span data-ttu-id="16079-544">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-544">section0:key1</span></span>
* <span data-ttu-id="16079-545">section1:key0</span><span class="sxs-lookup"><span data-stu-id="16079-545">section1:key0</span></span>
* <span data-ttu-id="16079-546">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-546">section1:key1</span></span>

<span data-ttu-id="16079-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="16079-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="16079-548">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="16079-548">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="16079-549">Konwencje</span><span class="sxs-lookup"><span data-stu-id="16079-549">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="16079-550">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="16079-550">Sources and providers</span></span>

<span data-ttu-id="16079-551">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-551">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="16079-552">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="16079-552">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="16079-553">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="16079-553">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="16079-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="16079-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> można wstrzyknąć do Razor stron <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC, <xref:Microsoft.AspNetCore.Mvc.Controller> Aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="16079-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="16079-556">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="16079-556">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="16079-557">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="16079-557">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="16079-558">Klucze</span><span class="sxs-lookup"><span data-stu-id="16079-558">Keys</span></span>

<span data-ttu-id="16079-559">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="16079-559">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="16079-560">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="16079-560">Keys are case-insensitive.</span></span> <span data-ttu-id="16079-561">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="16079-561">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="16079-562">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="16079-562">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="16079-563">Aby uzyskać więcej informacji na temat zduplikowanych kluczy JSON, zobacz [ten problem](https://github.com/dotnet/extensions/issues/2381)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="16079-563">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="16079-564">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="16079-564">Hierarchical keys</span></span>
  * <span data-ttu-id="16079-565">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="16079-565">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="16079-566">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="16079-566">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="16079-567">Podwójne podkreślenie ( `__` ) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="16079-567">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="16079-568">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="16079-568">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="16079-569">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-569">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="16079-570"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-570">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="16079-571">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="16079-571">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="16079-572">Wartości</span><span class="sxs-lookup"><span data-stu-id="16079-572">Values</span></span>

<span data-ttu-id="16079-573">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="16079-573">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="16079-574">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="16079-574">Values are strings.</span></span>
* <span data-ttu-id="16079-575">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="16079-575">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="16079-576">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="16079-576">Providers</span></span>

<span data-ttu-id="16079-577">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-577">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="16079-578">Dostawca</span><span class="sxs-lookup"><span data-stu-id="16079-578">Provider</span></span> | <span data-ttu-id="16079-579">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="16079-579">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="16079-580">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące*zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="16079-580">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="16079-581">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16079-581">Azure Key Vault</span></span> |
| <span data-ttu-id="16079-582">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="16079-582">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="16079-583">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="16079-583">Azure App Configuration</span></span> |
| [<span data-ttu-id="16079-584">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-584">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="16079-585">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-585">Command-line parameters</span></span> |
| [<span data-ttu-id="16079-586">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-586">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="16079-587">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="16079-587">Custom source</span></span> |
| [<span data-ttu-id="16079-588">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="16079-588">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="16079-589">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="16079-589">Environment variables</span></span> |
| [<span data-ttu-id="16079-590">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="16079-590">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="16079-591">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="16079-591">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="16079-592">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="16079-592">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="16079-593">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="16079-593">Directory files</span></span> |
| [<span data-ttu-id="16079-594">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="16079-594">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="16079-595">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="16079-595">In-memory collections</span></span> |
| <span data-ttu-id="16079-596">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tematy dotyczące*zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="16079-596">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="16079-597">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="16079-597">File in the user profile directory</span></span> |

<span data-ttu-id="16079-598">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="16079-598">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="16079-599">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="16079-599">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="16079-600">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="16079-600">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="16079-601">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="16079-601">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="16079-602">Pliki (*appsettings.js*, *appSettings. { Environment}. JSON*, gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="16079-602">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="16079-603">Usługa Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16079-603">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="16079-604">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="16079-604">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="16079-605">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="16079-605">Environment variables</span></span>
1. <span data-ttu-id="16079-606">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-606">Command-line arguments</span></span>

<span data-ttu-id="16079-607">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="16079-607">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="16079-608">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu programu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-608">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="16079-609">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="16079-609">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="16079-610">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="16079-610">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="16079-611">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="16079-611">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="16079-612">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="16079-612">ConfigureAppConfiguration</span></span>

<span data-ttu-id="16079-613">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="16079-613">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="16079-614">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-614">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="16079-615">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="16079-615">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="16079-616">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="16079-616">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="16079-617">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder` , najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="16079-617">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="16079-618">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="16079-618">Consume configuration during app startup</span></span>

<span data-ttu-id="16079-619">Konfiguracja dostarczona do aplikacji w programie `ConfigureAppConfiguration` jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="16079-619">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16079-620">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="16079-620">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="16079-621">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="16079-621">Command-line Configuration Provider</span></span>

<span data-ttu-id="16079-622"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="16079-622">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="16079-623">Aby uaktywnić konfigurację wiersza polecenia, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-623">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="16079-624">`AddCommandLine` jest wywoływana automatycznie, gdy `CreateDefaultBuilder(string [])` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="16079-624">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="16079-625">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="16079-625">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="16079-626">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="16079-626">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="16079-627">Opcjonalna konfiguracja *appsettings.jsna* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="16079-627">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="16079-628">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="16079-628">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="16079-629">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16079-629">Environment variables.</span></span>

<span data-ttu-id="16079-630">`CreateDefaultBuilder` dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="16079-630">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="16079-631">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="16079-631">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="16079-632">`CreateDefaultBuilder` działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="16079-632">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="16079-633">W związku z tym konfiguracja wiersza polecenia aktywowana przez program `CreateDefaultBuilder` może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="16079-633">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="16079-634">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-634">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="16079-635">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj jako `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="16079-635">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="16079-636">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="16079-636">**Example**</span></span>

<span data-ttu-id="16079-637">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="16079-637">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="16079-638">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="16079-638">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="16079-639">Podaj do polecenia argument wiersza polecenia `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="16079-639">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="16079-640">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="16079-640">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="16079-641">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="16079-641">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="16079-642">Argumenty</span><span class="sxs-lookup"><span data-stu-id="16079-642">Arguments</span></span>

<span data-ttu-id="16079-643">Wartość musi następować po znaku równości ( `=` ) lub klucz musi mieć prefiks ( `--` lub `/` ), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="16079-643">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="16079-644">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="16079-644">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="16079-645">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="16079-645">Key prefix</span></span>               | <span data-ttu-id="16079-646">Przykład</span><span class="sxs-lookup"><span data-stu-id="16079-646">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="16079-647">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="16079-647">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="16079-648">Dwie kreski ( `--` )</span><span class="sxs-lookup"><span data-stu-id="16079-648">Two dashes (`--`)</span></span>        | <span data-ttu-id="16079-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="16079-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="16079-650">Ukośnik ( `/` )</span><span class="sxs-lookup"><span data-stu-id="16079-650">Forward slash (`/`)</span></span>      | <span data-ttu-id="16079-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="16079-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="16079-652">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="16079-652">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="16079-653">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="16079-653">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="16079-654">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="16079-654">Switch mappings</span></span>

<span data-ttu-id="16079-655">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="16079-655">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="16079-656">Podczas ręcznego kompilowania konfiguracji za pomocą programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="16079-656">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="16079-657">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="16079-657">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="16079-658">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-658">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="16079-659">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="16079-659">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="16079-660">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="16079-660">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="16079-661">Przełączniki muszą zaczynać się kreską ( `-` ) lub podwójną kreską ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="16079-661">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="16079-662">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="16079-662">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="16079-663">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="16079-663">Create a switch mappings dictionary.</span></span> <span data-ttu-id="16079-664">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="16079-664">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="16079-665">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="16079-665">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="16079-666">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="16079-666">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="16079-667">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-667">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="16079-668">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="16079-668">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="16079-669">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="16079-669">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="16079-670">Klucz</span><span class="sxs-lookup"><span data-stu-id="16079-670">Key</span></span>       | <span data-ttu-id="16079-671">Wartość</span><span class="sxs-lookup"><span data-stu-id="16079-671">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="16079-672">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="16079-672">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="16079-673">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="16079-673">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="16079-674">Klucz</span><span class="sxs-lookup"><span data-stu-id="16079-674">Key</span></span>               | <span data-ttu-id="16079-675">Wartość</span><span class="sxs-lookup"><span data-stu-id="16079-675">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="16079-676">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="16079-676">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="16079-677"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="16079-677">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="16079-678">Aby uaktywnić konfigurację zmiennych środowiskowych, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-678">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="16079-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="16079-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="16079-680">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="16079-680">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="16079-681">`AddEnvironmentVariables` służy do ładowania zmiennych środowiskowych, które są poprzedzone `ASPNETCORE_` [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="16079-681">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="16079-682">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="16079-682">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="16079-683">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="16079-683">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="16079-684">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez wywołanie `AddEnvironmentVariables` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="16079-684">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="16079-685">Opcjonalna konfiguracja *appsettings.jsna* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="16079-685">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="16079-686">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="16079-686">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="16079-687">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="16079-687">Command-line arguments.</span></span>

<span data-ttu-id="16079-688">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="16079-688">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="16079-689">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="16079-689">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="16079-690">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddEnvironmentVariables` z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="16079-690">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="16079-691">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="16079-691">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="16079-692">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="16079-692">**Example**</span></span>

<span data-ttu-id="16079-693">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="16079-693">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="16079-694">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="16079-694">Run the sample app.</span></span> <span data-ttu-id="16079-695">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="16079-695">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="16079-696">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej środowiskowej `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="16079-696">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="16079-697">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj w `Development` przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="16079-697">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="16079-698">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16079-698">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="16079-699">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="16079-699">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="16079-700">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić `FilteredConfiguration` stronę na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="16079-700">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="16079-701">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="16079-701">Prefixes</span></span>

<span data-ttu-id="16079-702">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="16079-702">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="16079-703">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_` , podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-703">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="16079-704">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-704">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="16079-705">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16079-705">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="16079-706">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="16079-706">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="16079-707">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="16079-707">**Connection string prefixes**</span></span>

<span data-ttu-id="16079-708">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-708">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="16079-709">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="16079-709">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="16079-710">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="16079-710">Connection string prefix</span></span> | <span data-ttu-id="16079-711">Dostawca</span><span class="sxs-lookup"><span data-stu-id="16079-711">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="16079-712">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="16079-712">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="16079-713">MySQL</span><span class="sxs-lookup"><span data-stu-id="16079-713">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="16079-714">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="16079-714">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="16079-715">SQL Server</span><span class="sxs-lookup"><span data-stu-id="16079-715">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="16079-716">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="16079-716">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="16079-717">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="16079-717">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="16079-718">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="16079-718">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="16079-719">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="16079-719">Environment variable key</span></span> | <span data-ttu-id="16079-720">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-720">Converted configuration key</span></span> | <span data-ttu-id="16079-721">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="16079-721">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-722">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="16079-722">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-723">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="16079-723">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="16079-724">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="16079-724">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-725">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="16079-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="16079-726">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="16079-726">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="16079-727">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="16079-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="16079-728">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="16079-728">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="16079-729">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="16079-729">**Example**</span></span>

<span data-ttu-id="16079-730">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="16079-730">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="16079-731">Nazwa: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="16079-731">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="16079-732">Wartość: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="16079-732">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="16079-733">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config` , odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="16079-733">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="16079-734">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="16079-734">File Configuration Provider</span></span>

<span data-ttu-id="16079-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="16079-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="16079-736">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="16079-736">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="16079-737">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="16079-737">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="16079-738">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="16079-738">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="16079-739">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="16079-739">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="16079-740">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="16079-740">INI Configuration Provider</span></span>

<span data-ttu-id="16079-741"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="16079-741">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="16079-742">Aby uaktywnić konfigurację pliku INI, wywołaj <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-742">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="16079-743">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="16079-743">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="16079-744">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="16079-744">Overloads permit specifying:</span></span>

* <span data-ttu-id="16079-745">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="16079-745">Whether the file is optional.</span></span>
* <span data-ttu-id="16079-746">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-746">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="16079-747"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-747">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="16079-748">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="16079-748">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="16079-749">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="16079-749">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="16079-750">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="16079-750">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="16079-751">section0:key0</span><span class="sxs-lookup"><span data-stu-id="16079-751">section0:key0</span></span>
* <span data-ttu-id="16079-752">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-752">section0:key1</span></span>
* <span data-ttu-id="16079-753">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="16079-753">section1:subsection:key</span></span>
* <span data-ttu-id="16079-754">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="16079-754">section2:subsection0:key</span></span>
* <span data-ttu-id="16079-755">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="16079-755">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="16079-756">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="16079-756">JSON Configuration Provider</span></span>

<span data-ttu-id="16079-757"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="16079-757">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="16079-758">Aby uaktywnić konfigurację pliku JSON, wywołaj <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-758">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="16079-759">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="16079-759">Overloads permit specifying:</span></span>

* <span data-ttu-id="16079-760">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="16079-760">Whether the file is optional.</span></span>
* <span data-ttu-id="16079-761">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-761">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="16079-762"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-762">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="16079-763">`AddJsonFile` jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-763">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="16079-764">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="16079-764">The method is called to load configuration from:</span></span>

* <span data-ttu-id="16079-765">*appsettings.js*: ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="16079-765">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="16079-766">Wersja środowiska pliku może przesłonić wartości podane przez *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-766">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="16079-767">*appSettings. {Environment}. JSON*: wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="16079-767">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="16079-768">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="16079-768">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="16079-769">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="16079-769">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="16079-770">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16079-770">Environment variables.</span></span>
* <span data-ttu-id="16079-771">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="16079-771">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="16079-772">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="16079-772">Command-line arguments.</span></span>

<span data-ttu-id="16079-773">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="16079-773">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="16079-774">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="16079-774">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="16079-775">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *appsettings.jsw* i *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="16079-775">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="16079-776">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="16079-776">**Example**</span></span>

<span data-ttu-id="16079-777">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="16079-777">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="16079-778">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.jsna*:</span><span class="sxs-lookup"><span data-stu-id="16079-778">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="16079-779">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="16079-779">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="16079-780">W przypadku *appsettings.Development.js* w aplikacji przykładowej załadowano następujący plik:</span><span class="sxs-lookup"><span data-stu-id="16079-780">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="16079-781">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="16079-781">Run the sample app.</span></span> <span data-ttu-id="16079-782">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="16079-782">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="16079-783">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-783">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="16079-784">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="16079-784">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="16079-785">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="16079-785">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="16079-786">Otwórz *Właściwości/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-786">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="16079-787">W `ConfigurationSample` profilu Zmień wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej na `Production` .</span><span class="sxs-lookup"><span data-stu-id="16079-787">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="16079-788">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="16079-788">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="16079-789">Ustawienia w *appsettings.Development.js* nie przesłaniają już ustawień w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="16079-789">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="16079-790">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning` .</span><span class="sxs-lookup"><span data-stu-id="16079-790">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="16079-791">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="16079-791">XML Configuration Provider</span></span>

<span data-ttu-id="16079-792"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="16079-792">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="16079-793">Aby uaktywnić konfigurację pliku XML, wywołaj <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-793">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="16079-794">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="16079-794">Overloads permit specifying:</span></span>

* <span data-ttu-id="16079-795">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="16079-795">Whether the file is optional.</span></span>
* <span data-ttu-id="16079-796">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-796">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="16079-797"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-797">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="16079-798">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-798">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="16079-799">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-799">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="16079-800">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="16079-800">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="16079-801">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="16079-801">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="16079-802">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="16079-802">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="16079-803">section0:key0</span><span class="sxs-lookup"><span data-stu-id="16079-803">section0:key0</span></span>
* <span data-ttu-id="16079-804">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-804">section0:key1</span></span>
* <span data-ttu-id="16079-805">section1:key0</span><span class="sxs-lookup"><span data-stu-id="16079-805">section1:key0</span></span>
* <span data-ttu-id="16079-806">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-806">section1:key1</span></span>

<span data-ttu-id="16079-807">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="16079-807">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="16079-808">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="16079-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="16079-809">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="16079-809">section:section0:key:key0</span></span>
* <span data-ttu-id="16079-810">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-810">section:section0:key:key1</span></span>
* <span data-ttu-id="16079-811">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="16079-811">section:section1:key:key0</span></span>
* <span data-ttu-id="16079-812">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-812">section:section1:key:key1</span></span>

<span data-ttu-id="16079-813">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="16079-813">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="16079-814">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="16079-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="16079-815">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="16079-815">key:attribute</span></span>
* <span data-ttu-id="16079-816">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="16079-816">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="16079-817">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="16079-817">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="16079-818"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="16079-818">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="16079-819">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-819">The key is the file name.</span></span> <span data-ttu-id="16079-820">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="16079-820">The value contains the file's contents.</span></span> <span data-ttu-id="16079-821">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="16079-821">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="16079-822">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-822">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="16079-823">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="16079-823">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="16079-824">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="16079-824">Overloads permit specifying:</span></span>

* <span data-ttu-id="16079-825">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="16079-825">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="16079-826">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="16079-826">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="16079-827">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="16079-827">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="16079-828">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="16079-828">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="16079-829">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="16079-829">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="16079-830">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="16079-830">Memory Configuration Provider</span></span>

<span data-ttu-id="16079-831"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="16079-831">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="16079-832">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="16079-832">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="16079-833">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="16079-833">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="16079-834">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="16079-835">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-835">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="16079-836">Słownik jest używany z wywołaniem do `AddInMemoryCollection` zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-836">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="16079-837">GetValue</span><span class="sxs-lookup"><span data-stu-id="16079-837">GetValue</span></span>

<span data-ttu-id="16079-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="16079-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="16079-839">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="16079-839">An overload accepts a default value.</span></span>

<span data-ttu-id="16079-840">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="16079-840">The following example:</span></span>

* <span data-ttu-id="16079-841">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="16079-841">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="16079-842">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="16079-842">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="16079-843">Typ wartości `int` .</span><span class="sxs-lookup"><span data-stu-id="16079-843">Types the value as an `int`.</span></span>
* <span data-ttu-id="16079-844">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="16079-844">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="16079-845">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="16079-845">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="16079-846">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="16079-846">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="16079-847">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="16079-847">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="16079-848">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="16079-848">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="16079-849">section0:key0</span><span class="sxs-lookup"><span data-stu-id="16079-849">section0:key0</span></span>
* <span data-ttu-id="16079-850">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-850">section0:key1</span></span>
* <span data-ttu-id="16079-851">section1:key0</span><span class="sxs-lookup"><span data-stu-id="16079-851">section1:key0</span></span>
* <span data-ttu-id="16079-852">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-852">section1:key1</span></span>
* <span data-ttu-id="16079-853">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="16079-853">section2:subsection0:key0</span></span>
* <span data-ttu-id="16079-854">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-854">section2:subsection0:key1</span></span>
* <span data-ttu-id="16079-855">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="16079-855">section2:subsection1:key0</span></span>
* <span data-ttu-id="16079-856">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="16079-856">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="16079-857">GetSection</span><span class="sxs-lookup"><span data-stu-id="16079-857">GetSection</span></span>

<span data-ttu-id="16079-858">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="16079-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="16079-859">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1` , wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="16079-859">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="16079-860">`configSection`Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="16079-860">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="16079-861">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0` , wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="16079-861">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="16079-862">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="16079-862">`GetSection` never returns `null`.</span></span> <span data-ttu-id="16079-863">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="16079-863">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="16079-864">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="16079-864">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="16079-865">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="16079-865">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="16079-866">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="16079-866">GetChildren</span></span>

<span data-ttu-id="16079-867">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w programie `section2` uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="16079-867">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="16079-868">Exists</span><span class="sxs-lookup"><span data-stu-id="16079-868">Exists</span></span>

<span data-ttu-id="16079-869">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-869">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="16079-870">Dane przykładowe są spowodowane tym, że `sectionExists` `false` `section2:subsection2` w danych konfiguracji nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="16079-870">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="16079-871">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="16079-871">Bind to an object graph</span></span>

<span data-ttu-id="16079-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="16079-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="16079-873">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="16079-873">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="16079-874">Przykład zawiera `TvShow` model, którego obiekt zawiera obiekty `Metadata` i `Actors` klasy (*modele/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="16079-874">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="16079-875">Przykładowa aplikacja zawiera plik *tvshow.xml* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-875">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="16079-876">Konfiguracja jest powiązana z `TvShow` wykresem całego obiektu za pomocą `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="16079-876">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="16079-877">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="16079-877">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="16079-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="16079-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="16079-879">`Get<T>` jest wygodniejszy niż używanie `Bind` .</span><span class="sxs-lookup"><span data-stu-id="16079-879">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="16079-880">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="16079-880">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="16079-881">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="16079-881">Bind an array to a class</span></span>

<span data-ttu-id="16079-882">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="16079-882">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="16079-883"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-883">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="16079-884">Każdy format tablicy, który ujawnia segment klucza numerycznego ( `:0:` , `:1:` , &hellip; `:{n}:` ) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="16079-884">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="16079-885">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="16079-885">Binding is provided by convention.</span></span> <span data-ttu-id="16079-886">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="16079-886">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="16079-887">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="16079-887">**In-memory array processing**</span></span>

<span data-ttu-id="16079-888">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="16079-888">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="16079-889">Klucz</span><span class="sxs-lookup"><span data-stu-id="16079-889">Key</span></span>             | <span data-ttu-id="16079-890">Wartość</span><span class="sxs-lookup"><span data-stu-id="16079-890">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="16079-891">Tablica: wpisy: 0</span><span class="sxs-lookup"><span data-stu-id="16079-891">array:entries:0</span></span> | <span data-ttu-id="16079-892">value0</span><span class="sxs-lookup"><span data-stu-id="16079-892">value0</span></span> |
| <span data-ttu-id="16079-893">Tablica: wpisy: 1</span><span class="sxs-lookup"><span data-stu-id="16079-893">array:entries:1</span></span> | <span data-ttu-id="16079-894">sekwencj</span><span class="sxs-lookup"><span data-stu-id="16079-894">value1</span></span> |
| <span data-ttu-id="16079-895">Tablica: wpisy: 2</span><span class="sxs-lookup"><span data-stu-id="16079-895">array:entries:2</span></span> | <span data-ttu-id="16079-896">wartość2</span><span class="sxs-lookup"><span data-stu-id="16079-896">value2</span></span> |
| <span data-ttu-id="16079-897">Tablica: wpisy: 4</span><span class="sxs-lookup"><span data-stu-id="16079-897">array:entries:4</span></span> | <span data-ttu-id="16079-898">value4</span><span class="sxs-lookup"><span data-stu-id="16079-898">value4</span></span> |
| <span data-ttu-id="16079-899">Tablica: wpisy: 5</span><span class="sxs-lookup"><span data-stu-id="16079-899">array:entries:5</span></span> | <span data-ttu-id="16079-900">value5</span><span class="sxs-lookup"><span data-stu-id="16079-900">value5</span></span> |

<span data-ttu-id="16079-901">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="16079-901">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="16079-902">Tablica pomija wartość dla indeksu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="16079-902">The array skips a value for index &num;3.</span></span> <span data-ttu-id="16079-903">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="16079-903">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="16079-904">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-904">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="16079-905">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="16079-905">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="16079-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="16079-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="16079-907">Obiekt powiązany, wystąpienie elementu `ArrayExample` , otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-907">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="16079-908">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="16079-908">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="16079-909">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="16079-909">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="16079-910">0</span><span class="sxs-lookup"><span data-stu-id="16079-910">0</span></span>                            | <span data-ttu-id="16079-911">value0</span><span class="sxs-lookup"><span data-stu-id="16079-911">value0</span></span>                       |
| <span data-ttu-id="16079-912">1</span><span class="sxs-lookup"><span data-stu-id="16079-912">1</span></span>                            | <span data-ttu-id="16079-913">sekwencj</span><span class="sxs-lookup"><span data-stu-id="16079-913">value1</span></span>                       |
| <span data-ttu-id="16079-914">2</span><span class="sxs-lookup"><span data-stu-id="16079-914">2</span></span>                            | <span data-ttu-id="16079-915">wartość2</span><span class="sxs-lookup"><span data-stu-id="16079-915">value2</span></span>                       |
| <span data-ttu-id="16079-916">3</span><span class="sxs-lookup"><span data-stu-id="16079-916">3</span></span>                            | <span data-ttu-id="16079-917">value4</span><span class="sxs-lookup"><span data-stu-id="16079-917">value4</span></span>                       |
| <span data-ttu-id="16079-918">4</span><span class="sxs-lookup"><span data-stu-id="16079-918">4</span></span>                            | <span data-ttu-id="16079-919">value5</span><span class="sxs-lookup"><span data-stu-id="16079-919">value5</span></span>                       |

<span data-ttu-id="16079-920">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="16079-920">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="16079-921">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="16079-921">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="16079-922">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="16079-922">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="16079-923">Brakujący element konfiguracji dla indeksu &num; 3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-923">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="16079-924">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="16079-924">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="16079-925">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="16079-925">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="16079-926">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="16079-926">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="16079-927">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-927">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="16079-928">Klucz</span><span class="sxs-lookup"><span data-stu-id="16079-928">Key</span></span>             | <span data-ttu-id="16079-929">Wartość</span><span class="sxs-lookup"><span data-stu-id="16079-929">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="16079-930">Tablica: wpisy: 3</span><span class="sxs-lookup"><span data-stu-id="16079-930">array:entries:3</span></span> | <span data-ttu-id="16079-931">Wartość3</span><span class="sxs-lookup"><span data-stu-id="16079-931">value3</span></span> |

<span data-ttu-id="16079-932">Jeśli `ArrayExample` wystąpienie klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num; 3, `ArrayExample.Entries` Tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="16079-932">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="16079-933">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="16079-933">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="16079-934">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="16079-934">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="16079-935">0</span><span class="sxs-lookup"><span data-stu-id="16079-935">0</span></span>                            | <span data-ttu-id="16079-936">value0</span><span class="sxs-lookup"><span data-stu-id="16079-936">value0</span></span>                       |
| <span data-ttu-id="16079-937">1</span><span class="sxs-lookup"><span data-stu-id="16079-937">1</span></span>                            | <span data-ttu-id="16079-938">sekwencj</span><span class="sxs-lookup"><span data-stu-id="16079-938">value1</span></span>                       |
| <span data-ttu-id="16079-939">2</span><span class="sxs-lookup"><span data-stu-id="16079-939">2</span></span>                            | <span data-ttu-id="16079-940">wartość2</span><span class="sxs-lookup"><span data-stu-id="16079-940">value2</span></span>                       |
| <span data-ttu-id="16079-941">3</span><span class="sxs-lookup"><span data-stu-id="16079-941">3</span></span>                            | <span data-ttu-id="16079-942">Wartość3</span><span class="sxs-lookup"><span data-stu-id="16079-942">value3</span></span>                       |
| <span data-ttu-id="16079-943">4</span><span class="sxs-lookup"><span data-stu-id="16079-943">4</span></span>                            | <span data-ttu-id="16079-944">value4</span><span class="sxs-lookup"><span data-stu-id="16079-944">value4</span></span>                       |
| <span data-ttu-id="16079-945">5</span><span class="sxs-lookup"><span data-stu-id="16079-945">5</span></span>                            | <span data-ttu-id="16079-946">value5</span><span class="sxs-lookup"><span data-stu-id="16079-946">value5</span></span>                       |

<span data-ttu-id="16079-947">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="16079-947">**JSON array processing**</span></span>

<span data-ttu-id="16079-948">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="16079-948">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="16079-949">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="16079-949">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="16079-950">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="16079-950">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="16079-951">Klucz</span><span class="sxs-lookup"><span data-stu-id="16079-951">Key</span></span>                     | <span data-ttu-id="16079-952">Wartość</span><span class="sxs-lookup"><span data-stu-id="16079-952">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="16079-953">json_array: klucz</span><span class="sxs-lookup"><span data-stu-id="16079-953">json_array:key</span></span>          | <span data-ttu-id="16079-954">wartośća</span><span class="sxs-lookup"><span data-stu-id="16079-954">valueA</span></span> |
| <span data-ttu-id="16079-955">json_array: podsekcja: 0</span><span class="sxs-lookup"><span data-stu-id="16079-955">json_array:subsection:0</span></span> | <span data-ttu-id="16079-956">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="16079-956">valueB</span></span> |
| <span data-ttu-id="16079-957">json_array: podsekcja: 1</span><span class="sxs-lookup"><span data-stu-id="16079-957">json_array:subsection:1</span></span> | <span data-ttu-id="16079-958">valueC</span><span class="sxs-lookup"><span data-stu-id="16079-958">valueC</span></span> |
| <span data-ttu-id="16079-959">json_array: podsekcja: 2</span><span class="sxs-lookup"><span data-stu-id="16079-959">json_array:subsection:2</span></span> | <span data-ttu-id="16079-960">Znajdując</span><span class="sxs-lookup"><span data-stu-id="16079-960">valueD</span></span> |

<span data-ttu-id="16079-961">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="16079-961">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="16079-962">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA` .</span><span class="sxs-lookup"><span data-stu-id="16079-962">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="16079-963">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="16079-963">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="16079-964">`JsonArrayExample.Subsection` Indeks</span><span class="sxs-lookup"><span data-stu-id="16079-964">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="16079-965">`JsonArrayExample.Subsection` Wartościami</span><span class="sxs-lookup"><span data-stu-id="16079-965">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="16079-966">0</span><span class="sxs-lookup"><span data-stu-id="16079-966">0</span></span>                                   | <span data-ttu-id="16079-967">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="16079-967">valueB</span></span>                              |
| <span data-ttu-id="16079-968">1</span><span class="sxs-lookup"><span data-stu-id="16079-968">1</span></span>                                   | <span data-ttu-id="16079-969">valueC</span><span class="sxs-lookup"><span data-stu-id="16079-969">valueC</span></span>                              |
| <span data-ttu-id="16079-970">2</span><span class="sxs-lookup"><span data-stu-id="16079-970">2</span></span>                                   | <span data-ttu-id="16079-971">Znajdując</span><span class="sxs-lookup"><span data-stu-id="16079-971">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="16079-972">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="16079-972">Custom configuration provider</span></span>

<span data-ttu-id="16079-973">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="16079-973">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="16079-974">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="16079-974">The provider has the following characteristics:</span></span>

* <span data-ttu-id="16079-975">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="16079-975">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="16079-976">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="16079-976">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="16079-977">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="16079-977">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="16079-978">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="16079-978">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="16079-979">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-979">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="16079-980">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="16079-980">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="16079-981">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-981">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="16079-982">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="16079-982">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="16079-983">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="16079-984">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="16079-984">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="16079-985">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="16079-986">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="16079-986">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="16079-987">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="16079-987">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="16079-988">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="16079-989">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="16079-989">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="16079-990">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-990">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="16079-991">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="16079-991">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="16079-992">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="16079-992">Access configuration during startup</span></span>

<span data-ttu-id="16079-993">Wsuń `IConfiguration` do `Startup` konstruktora, aby uzyskać dostęp do wartości konfiguracyjnych w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="16079-993">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16079-994">Aby uzyskać dostęp do konfiguracji w programie `Startup.Configure` , należy wstrzyknąć `IConfiguration` bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="16079-994">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="16079-995">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="16079-995">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="16079-996">Konfiguracja dostępu na Razor stronie stron lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="16079-996">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="16079-997">Aby uzyskać dostęp do ustawień konfiguracji na Razor stronie stron lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [ przestrzeni nazwMicrosoft.Extensions.Configwersja](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="16079-997">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="16079-998">Na Razor stronie stron:</span><span class="sxs-lookup"><span data-stu-id="16079-998">In a Razor Pages page:</span></span>

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

<span data-ttu-id="16079-999">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="16079-999">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="16079-1000">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="16079-1000">Add configuration from an external assembly</span></span>

<span data-ttu-id="16079-1001"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16079-1001">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="16079-1002">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="16079-1002">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16079-1003">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="16079-1003">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end