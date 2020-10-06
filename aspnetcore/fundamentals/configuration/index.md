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
ms.openlocfilehash: 7565ede55acd936072fc1930918d46808548f287
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762350"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9f7ad-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f7ad-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9f7ad-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9f7ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f7ad-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="9f7ad-106">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="9f7ad-107">Pliki ustawień, takie jak *appsettings.jswłączone*</span><span class="sxs-lookup"><span data-stu-id="9f7ad-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="9f7ad-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-108">Environment variables</span></span>
* <span data-ttu-id="9f7ad-109">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f7ad-109">Azure Key Vault</span></span>
* <span data-ttu-id="9f7ad-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9f7ad-110">Azure App Configuration</span></span>
* <span data-ttu-id="9f7ad-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-111">Command-line arguments</span></span>
* <span data-ttu-id="9f7ad-112">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="9f7ad-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="9f7ad-113">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="9f7ad-113">Directory files</span></span>
* <span data-ttu-id="9f7ad-114">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="9f7ad-114">In-memory .NET objects</span></span>

<span data-ttu-id="9f7ad-115">Ten temat zawiera informacje dotyczące konfiguracji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="9f7ad-116">Aby uzyskać informacje na temat korzystania z konfiguracji w aplikacjach konsolowych, zobacz [Konfiguracja platformy .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="9f7ad-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f7ad-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="9f7ad-118">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="9f7ad-118">Default configuration</span></span>

<span data-ttu-id="9f7ad-119">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="9f7ad-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="9f7ad-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący element `IConfiguration` jako źródło.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="9f7ad-122">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="9f7ad-123">[appsettings.js](#appsettingsjson) przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="9f7ad-124">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="9f7ad-125">Na przykład *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. *kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-125">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="9f7ad-126">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="9f7ad-127">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="9f7ad-128">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="9f7ad-129">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="9f7ad-130">Na przykład jeśli `MyKey` jest ustawiona zarówno w *appsettings.js* , jak i w środowisku, zostanie użyta wartość środowiska.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="9f7ad-131">Przy użyciu domyślnych dostawców konfiguracji  [dostawca konfiguracji wiersza polecenia](#clcp) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="9f7ad-132">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="9f7ad-133">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="9f7ad-134">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="9f7ad-134">appsettings.json</span></span>

<span data-ttu-id="9f7ad-135">Rozważmy następujące *appsettings.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-135">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="9f7ad-136">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-136">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-137">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-137">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="9f7ad-138">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9f7ad-138">*appsettings.json*</span></span>
1. <span data-ttu-id="9f7ad-139">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-139">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="9f7ad-140">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-140">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="9f7ad-141">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-141">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9f7ad-142">*AppSettings*. `Environment` . wartości *JSON* przesłaniają klucze w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-142">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="9f7ad-143">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-143">For example, by default:</span></span>

* <span data-ttu-id="9f7ad-144">W obszarze programowanie, *AppSettings*. ***Programowanie***. Konfiguracja *JSON* zastępuje wartości znalezione w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-144">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="9f7ad-145">W obszarze produkcja, *AppSettings*. ***Środowisko produkcyjne***. Konfiguracja *JSON* zastępuje wartości znalezione w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-145">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="9f7ad-146">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-146">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="9f7ad-147">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-147">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="9f7ad-148">Korzystając z konfiguracji [domyślnej](#default) , *appsettings.jsna* i *appSettings.* `Environment` pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-148">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="9f7ad-149">Zmiany wprowadzone w *appsettings.js* i *appSettings.* `Environment` plik *. JSON* ***po*** uruchomieniu aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-149">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="9f7ad-150">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-150">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="9f7ad-151">Łączenie kolekcji usług</span><span class="sxs-lookup"><span data-stu-id="9f7ad-151">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="9f7ad-152">Security and Secret Manager</span><span class="sxs-lookup"><span data-stu-id="9f7ad-152">Security and secret manager</span></span>

<span data-ttu-id="9f7ad-153">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-153">Configuration data guidelines:</span></span>

* <span data-ttu-id="9f7ad-154">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-154">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="9f7ad-155">Za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-155">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="9f7ad-156">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-156">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9f7ad-157">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-157">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9f7ad-158">[Domyślnie](#default)program [Secret Manager](xref:security/app-secrets) odczytuje ustawienia konfiguracji po *appsettings.jsna* i *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-158">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="9f7ad-159">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-159">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9f7ad-160"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-160"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9f7ad-161">Menedżer wpisów tajnych używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-161">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="9f7ad-162">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-162">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9f7ad-163">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-163">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="9f7ad-164">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-164">Environment variables</span></span>

<span data-ttu-id="9f7ad-165">Przy użyciu konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość po czytaniu *appsettings.jsw*, *appSettings.* `Environment` *. JSON*i [Menedżer wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-165">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9f7ad-166">W związku z tym kluczowe wartości są odczytywane z wartości zastąpienia środowiska odczytywane z *appsettings.jsw*, *appSettings.* `Environment` *. JSON*i Menedżer wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-166">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9f7ad-167">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-167">The following `set` commands:</span></span>

* <span data-ttu-id="9f7ad-168">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-168">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="9f7ad-169">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-169">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="9f7ad-170">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-170">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="9f7ad-171">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-171">The preceding environment settings:</span></span>

* <span data-ttu-id="9f7ad-172">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-172">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="9f7ad-173">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-173">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="9f7ad-174">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-174">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="9f7ad-175">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-175">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="9f7ad-176">`/M` ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-176">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="9f7ad-177">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-177">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="9f7ad-178">Aby sprawdzić, czy poprzednie polecenia zastępują *appsettings.js* i *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-178">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="9f7ad-179">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-179">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="9f7ad-180">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i wprowadź `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-180">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="9f7ad-181">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-181">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="9f7ad-182">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-182">In the preceding code:</span></span>

* <span data-ttu-id="9f7ad-183">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-183">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="9f7ad-184">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-184">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="9f7ad-185">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-185">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="9f7ad-186">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-186">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="9f7ad-187">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-187">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="9f7ad-188">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-188">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="9f7ad-189">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-189">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="9f7ad-190">`DOTNET_` `ASPNETCORE_` Prefiksy i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-190">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="9f7ad-191">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-191">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="9f7ad-192">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-192">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="9f7ad-193">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-193">Azure App Service application settings are:</span></span>

* <span data-ttu-id="9f7ad-194">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-194">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="9f7ad-195">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-195">Exposed as environment variables.</span></span>

<span data-ttu-id="9f7ad-196">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-196">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9f7ad-197">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-197">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="9f7ad-198">Zmienne środowiskowe ustawione w launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="9f7ad-198">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="9f7ad-199">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-199">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="9f7ad-200">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-200">Command-line</span></span>

<span data-ttu-id="9f7ad-201">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-201">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="9f7ad-202">*appsettings.jsna* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-202">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="9f7ad-203">Wpisy [tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-203">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9f7ad-204">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-204">Environment variables.</span></span>

<span data-ttu-id="9f7ad-205">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-205">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="9f7ad-206">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-206">Command-line arguments</span></span>

<span data-ttu-id="9f7ad-207">Następujące polecenie ustawia klucze i wartości przy użyciu `=` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-207">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="9f7ad-208">Następujące polecenie ustawia klucze i wartości przy użyciu `/` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-208">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="9f7ad-209">Następujące polecenie ustawia klucze i wartości przy użyciu `--` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-209">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="9f7ad-210">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-210">The key value:</span></span>

* <span data-ttu-id="9f7ad-211">Musi `=` być zgodna lub musi mieć prefiks lub, gdy wartość znajduje się w `--` `/` miejscu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-211">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="9f7ad-212">Nie jest wymagane, jeśli `=` jest używany.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-212">Isn't required if `=` is used.</span></span> <span data-ttu-id="9f7ad-213">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-213">For example, `MySetting=`.</span></span>

<span data-ttu-id="9f7ad-214">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które są używane `=` z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-214">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="9f7ad-215">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="9f7ad-215">Switch mappings</span></span>

<span data-ttu-id="9f7ad-216">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-216">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="9f7ad-217">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-217">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9f7ad-218">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-218">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9f7ad-219">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-219">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9f7ad-220">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-220">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9f7ad-221">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-221">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9f7ad-222">Przełączniki muszą zaczynać się od `-` lub `--` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-222">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="9f7ad-223">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-223">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9f7ad-224">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-224">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="9f7ad-225">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-225">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-226">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-226">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="9f7ad-227">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-227">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9f7ad-228">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-228">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f7ad-229">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-229">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9f7ad-230">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-230">Hierarchical configuration data</span></span>

<span data-ttu-id="9f7ad-231">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-231">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9f7ad-232">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *appsettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-232">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="9f7ad-233">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-233">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-234">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-234">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="9f7ad-235">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-235">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="9f7ad-236"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-236"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9f7ad-237">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-237">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="9f7ad-238">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-238">Configuration keys and values</span></span>

<span data-ttu-id="9f7ad-239">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-239">Configuration keys:</span></span>

* <span data-ttu-id="9f7ad-240">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-240">Are case-insensitive.</span></span> <span data-ttu-id="9f7ad-241">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-241">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9f7ad-242">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-242">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="9f7ad-243">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-243">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="9f7ad-244">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="9f7ad-244">Hierarchical keys</span></span>
  * <span data-ttu-id="9f7ad-245">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-245">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9f7ad-246">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-246">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9f7ad-247">Podwójne podkreślenie, `__` ,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-247">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="9f7ad-248">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-248">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="9f7ad-249">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-249">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9f7ad-250"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-250">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9f7ad-251">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-251">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="9f7ad-252">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-252">Configuration values:</span></span>

* <span data-ttu-id="9f7ad-253">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-253">Are strings.</span></span>
* <span data-ttu-id="9f7ad-254">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-254">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="9f7ad-255">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-255">Configuration providers</span></span>

<span data-ttu-id="9f7ad-256">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-256">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9f7ad-257">Dostawca</span><span class="sxs-lookup"><span data-stu-id="9f7ad-257">Provider</span></span> | <span data-ttu-id="9f7ad-258">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="9f7ad-258">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="9f7ad-259">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f7ad-259">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="9f7ad-260">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f7ad-260">Azure Key Vault</span></span> |
| [<span data-ttu-id="9f7ad-261">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9f7ad-261">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="9f7ad-262">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9f7ad-262">Azure App Configuration</span></span> |
| [<span data-ttu-id="9f7ad-263">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-263">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="9f7ad-264">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-264">Command-line parameters</span></span> |
| [<span data-ttu-id="9f7ad-265">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-265">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="9f7ad-266">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-266">Custom source</span></span> |
| [<span data-ttu-id="9f7ad-267">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-267">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="9f7ad-268">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-268">Environment variables</span></span> |
| [<span data-ttu-id="9f7ad-269">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-269">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="9f7ad-270">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="9f7ad-270">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="9f7ad-271">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-271">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="9f7ad-272">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="9f7ad-272">Directory files</span></span> |
| [<span data-ttu-id="9f7ad-273">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="9f7ad-273">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="9f7ad-274">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="9f7ad-274">In-memory collections</span></span> |
| [<span data-ttu-id="9f7ad-275">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-275">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="9f7ad-276">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="9f7ad-276">File in the user profile directory</span></span> |

<span data-ttu-id="9f7ad-277">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-277">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="9f7ad-278">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-278">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9f7ad-279">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-279">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9f7ad-280">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9f7ad-280">*appsettings.json*</span></span>
1. <span data-ttu-id="9f7ad-281">*AppSettings*. `Environment` . *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="9f7ad-281">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="9f7ad-282">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-282">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="9f7ad-283">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-283">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="9f7ad-284">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-284">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="9f7ad-285">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-285">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9f7ad-286">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-286">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="9f7ad-287">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-287">Connection string prefixes</span></span>

<span data-ttu-id="9f7ad-288">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-288">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="9f7ad-289">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-289">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9f7ad-290">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-290">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9f7ad-291">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-291">Connection string prefix</span></span> | <span data-ttu-id="9f7ad-292">Dostawca</span><span class="sxs-lookup"><span data-stu-id="9f7ad-292">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="9f7ad-293">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-293">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="9f7ad-294">MySQL</span><span class="sxs-lookup"><span data-stu-id="9f7ad-294">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="9f7ad-295">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="9f7ad-295">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="9f7ad-296">SQL Server</span><span class="sxs-lookup"><span data-stu-id="9f7ad-296">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="9f7ad-297">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-297">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9f7ad-298">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-298">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9f7ad-299">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-299">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9f7ad-300">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="9f7ad-300">Environment variable key</span></span> | <span data-ttu-id="9f7ad-301">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-301">Converted configuration key</span></span> | <span data-ttu-id="9f7ad-302">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-302">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-303">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-303">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-304">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9f7ad-305">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-305">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-306">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9f7ad-307">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-307">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-308">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9f7ad-309">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-309">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="9f7ad-310">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-310">File configuration provider</span></span>

<span data-ttu-id="9f7ad-311"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-311"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9f7ad-312">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-312">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="9f7ad-313">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="9f7ad-313">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9f7ad-314">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="9f7ad-314">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="9f7ad-315">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="9f7ad-315">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9f7ad-316">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="9f7ad-316">INI configuration provider</span></span>

<span data-ttu-id="9f7ad-317"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-317">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9f7ad-318">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-318">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="9f7ad-319">W powyższym kodzie ustawienia w *MyIniConfig.ini* i  *MyIniConfig*. `Environment` . pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-319">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="9f7ad-320">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-320">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="9f7ad-321">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-321">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9f7ad-322">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-322">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="9f7ad-323">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-323">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="9f7ad-324">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="9f7ad-324">JSON configuration provider</span></span>

<span data-ttu-id="9f7ad-325"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-325">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="9f7ad-326">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-326">Overloads can specify:</span></span>

* <span data-ttu-id="9f7ad-327">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-327">Whether the file is optional.</span></span>
* <span data-ttu-id="9f7ad-328">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-328">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="9f7ad-329">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-329">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="9f7ad-330">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-330">The preceding code:</span></span>

* <span data-ttu-id="9f7ad-331">Konfiguruje dostawcę konfiguracji JSON w celu załadowania *MyConfig.jsw* pliku z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-331">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="9f7ad-332">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-332">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="9f7ad-333">`reloadOnChange: true` : Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-333">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="9f7ad-334">Odczytuje [domyślnych dostawców konfiguracji](#default) przed *MyConfig.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-334">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="9f7ad-335">Ustawienia w *MyConfig.js* ustawienia przesłania pliku w domyślnych dostawcach konfiguracji, w tym [dostawca konfiguracji zmiennych środowiskowych](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-335">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9f7ad-336">Zwykle ***nie*** chcesz, aby niestandardowy plik JSON zastępujący wartości ustawione w [zmiennej środowiskowej dostawcy konfiguracji](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-336">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9f7ad-337">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-337">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="9f7ad-338">W powyższym kodzie ustawienia w *MyConfig.jsna* i  *konfiguracji*. `Environment` .. pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-338">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="9f7ad-339">Zastąp ustawienia w *appsettings.js* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-339">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="9f7ad-340">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-340">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9f7ad-341">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *MyConfig.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-341">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="9f7ad-342">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-342">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="9f7ad-343">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="9f7ad-343">XML configuration provider</span></span>

<span data-ttu-id="9f7ad-344"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-344">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9f7ad-345">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-345">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="9f7ad-346">W powyższym kodzie ustawienia w *MyXMLFile.xml* i  *MyXMLFile*. `Environment` . pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-346">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="9f7ad-347">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-347">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="9f7ad-348">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-348">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9f7ad-349">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-349">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="9f7ad-350">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-350">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-351">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-351">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="9f7ad-352">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-352">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-353">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-353">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9f7ad-354">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-354">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9f7ad-355">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="9f7ad-355">key:attribute</span></span>
* <span data-ttu-id="9f7ad-356">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="9f7ad-356">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9f7ad-357">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-357">Key-per-file configuration provider</span></span>

<span data-ttu-id="9f7ad-358"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-358">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9f7ad-359">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-359">The key is the file name.</span></span> <span data-ttu-id="9f7ad-360">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-360">The value contains the file's contents.</span></span> <span data-ttu-id="9f7ad-361">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-361">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9f7ad-362">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-362">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9f7ad-363">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-363">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9f7ad-364">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-364">Overloads permit specifying:</span></span>

* <span data-ttu-id="9f7ad-365">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-365">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9f7ad-366">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-366">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9f7ad-367">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-367">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9f7ad-368">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-368">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9f7ad-369">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-369">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="9f7ad-370">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="9f7ad-370">Memory configuration provider</span></span>

<span data-ttu-id="9f7ad-371"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-371">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9f7ad-372">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-372">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="9f7ad-373">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-373">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-374">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-374">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="9f7ad-375">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-375">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="9f7ad-376">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-376">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="9f7ad-377">GetValue</span><span class="sxs-lookup"><span data-stu-id="9f7ad-377">GetValue</span></span>

<span data-ttu-id="9f7ad-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-379">W powyższym kodzie, jeśli `NumberKey` nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-379">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9f7ad-380">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="9f7ad-380">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9f7ad-381">W poniższych przykładach należy wziąć pod uwagę następujące *MySubsection.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-381">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="9f7ad-382">Poniższy kod dodaje *MySubsection.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-382">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="9f7ad-383">GetSection</span><span class="sxs-lookup"><span data-stu-id="9f7ad-383">GetSection</span></span>

<span data-ttu-id="9f7ad-384">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-384">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9f7ad-385">Poniższy kod zwraca wartości dla `section1` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-385">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-386">Poniższy kod zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-386">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-387">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-387">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9f7ad-388">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-388">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9f7ad-389">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-389">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9f7ad-390">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-390">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="9f7ad-391">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="9f7ad-391">GetChildren and Exists</span></span>

<span data-ttu-id="9f7ad-392">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-392">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-393">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-393">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="9f7ad-394">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="9f7ad-394">Bind an array</span></span>

<span data-ttu-id="9f7ad-395">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-395">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9f7ad-396">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-396">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="9f7ad-397">Rozważ *MyArray.jsna* podstawie pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="9f7ad-397">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="9f7ad-398">Poniższy kod dodaje *MyArray.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-398">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="9f7ad-399">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-399">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-400">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-400">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="9f7ad-401">W poprzednich danych wyjściowych indeks 3 ma wartość `value40` odpowiadającą wartości `"4": "value40",` w *MyArray.jsna*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-401">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="9f7ad-402">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-402">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="9f7ad-403">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-403">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="9f7ad-404">Poniższy kod ładuje `array:entries` konfigurację z użyciem <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-404">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="9f7ad-405">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-405">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-406">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-406">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="9f7ad-407">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-407">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9f7ad-408">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-408">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9f7ad-409">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-409">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9f7ad-410">Brakujący element konfiguracji dla indeksu &num; 3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje &num; parę klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-410">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="9f7ad-411">Rozważmy następujące *Value3.js* pliku z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-411">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="9f7ad-412">Poniższy kod zawiera konfigurację dla *Value3.js* i `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-412">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="9f7ad-413">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-413">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-414">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-414">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="9f7ad-415">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-415">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="9f7ad-416">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-416">Custom configuration provider</span></span>

<span data-ttu-id="9f7ad-417">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-417">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9f7ad-418">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-418">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9f7ad-419">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-419">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9f7ad-420">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-420">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9f7ad-421">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-421">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9f7ad-422">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-422">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9f7ad-423">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-423">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9f7ad-424">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-424">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9f7ad-425">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-425">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9f7ad-426">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-426">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9f7ad-427">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-427">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9f7ad-428">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-428">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9f7ad-429">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-429">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9f7ad-430">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-430">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9f7ad-431">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-431">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="9f7ad-432">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-432">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="9f7ad-433">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-433">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9f7ad-434">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-434">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9f7ad-435">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-435">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9f7ad-436">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-436">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="9f7ad-437">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="9f7ad-437">Access configuration in Startup</span></span>

<span data-ttu-id="9f7ad-438">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-438">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="9f7ad-439">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-439">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="9f7ad-440">Konfiguracja dostępu na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="9f7ad-440">Access configuration in Razor Pages</span></span>

<span data-ttu-id="9f7ad-441">Poniższy kod przedstawia dane konfiguracji na Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-441">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="9f7ad-442">W poniższym kodzie `MyOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-442">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="9f7ad-443">Następujące oznakowanie używa [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy do rozwiązywania i wyświetlania wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-443">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="9f7ad-444">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="9f7ad-444">Access configuration in a MVC view file</span></span>

<span data-ttu-id="9f7ad-445">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-445">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="9f7ad-446">Konfigurowanie opcji za pomocą delegata</span><span class="sxs-lookup"><span data-stu-id="9f7ad-446">Configure options with a delegate</span></span>

<span data-ttu-id="9f7ad-447">Opcje skonfigurowane w wartościach zastąpień delegatów ustawionych w dostawcach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-447">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="9f7ad-448">Konfigurowanie opcji za pomocą delegata jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-448">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9f7ad-449">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-449">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9f7ad-450">Używa delegata do konfigurowania wartości dla `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-450">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="9f7ad-451">Poniższy kod wyświetla wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-451">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="9f7ad-452">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w *appsettings.jsna* , a następnie zastąpione przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-452">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9f7ad-453">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-453">Host versus app configuration</span></span>

<span data-ttu-id="9f7ad-454">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-454">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9f7ad-455">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-455">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9f7ad-456">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-456">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9f7ad-457">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-457">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9f7ad-458">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-458">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="9f7ad-459">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="9f7ad-459">Default host configuration</span></span>

<span data-ttu-id="9f7ad-460">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-460">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="9f7ad-461">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-461">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9f7ad-462">Zmienne środowiskowe poprzedzone znakiem `DOTNET_` (na przykład `DOTNET_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-462">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="9f7ad-463">Prefiks ( `DOTNET_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-463">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9f7ad-464">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-464">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9f7ad-465">Konfiguracja domyślna hosta sieci Web ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="9f7ad-465">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="9f7ad-466">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-466">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="9f7ad-467">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-467">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="9f7ad-468">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` zmienna środowiskowa jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-468">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="9f7ad-469">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-469">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9f7ad-470">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="9f7ad-470">Other configuration</span></span>

<span data-ttu-id="9f7ad-471">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-471">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9f7ad-472">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-472">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9f7ad-473">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-473">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9f7ad-474">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-474">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9f7ad-475">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-475">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9f7ad-476">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-476">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9f7ad-477">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-477">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="9f7ad-478">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-478">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9f7ad-479">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="9f7ad-479">Add configuration from an external assembly</span></span>

<span data-ttu-id="9f7ad-480"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-480">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9f7ad-481">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-481">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f7ad-482">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9f7ad-482">Additional resources</span></span>

* [<span data-ttu-id="9f7ad-483">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-483">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f7ad-484">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-484">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9f7ad-485">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-485">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9f7ad-486">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f7ad-486">Azure Key Vault</span></span>
* <span data-ttu-id="9f7ad-487">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9f7ad-487">Azure App Configuration</span></span>
* <span data-ttu-id="9f7ad-488">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-488">Command-line arguments</span></span>
* <span data-ttu-id="9f7ad-489">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="9f7ad-489">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9f7ad-490">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="9f7ad-490">Directory files</span></span>
* <span data-ttu-id="9f7ad-491">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-491">Environment variables</span></span>
* <span data-ttu-id="9f7ad-492">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="9f7ad-492">In-memory .NET objects</span></span>
* <span data-ttu-id="9f7ad-493">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="9f7ad-493">Settings files</span></span>

<span data-ttu-id="9f7ad-494">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-494">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9f7ad-495">Przykłady kodu, które obserwują i w przykładowej aplikacji używają <xref:Microsoft.Extensions.Configuration> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-495">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9f7ad-496">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-496">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9f7ad-497">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-497">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="9f7ad-498">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-498">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9f7ad-499">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f7ad-499">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9f7ad-500">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-500">Host versus app configuration</span></span>

<span data-ttu-id="9f7ad-501">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-501">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9f7ad-502">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-502">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9f7ad-503">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-503">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9f7ad-504">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-504">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9f7ad-505">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-505">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9f7ad-506">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="9f7ad-506">Other configuration</span></span>

<span data-ttu-id="9f7ad-507">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-507">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9f7ad-508">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-508">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9f7ad-509">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-509">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9f7ad-510">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-510">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9f7ad-511">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-511">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9f7ad-512">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-512">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9f7ad-513">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-513">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9f7ad-514">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="9f7ad-514">Default configuration</span></span>

<span data-ttu-id="9f7ad-515">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych szablonów dotnet](/dotnet/core/tools/dotnet-new) są wywoływane <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-515">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9f7ad-516">`CreateDefaultBuilder` zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-516">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9f7ad-517">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-517">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9f7ad-518">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-518">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="9f7ad-519">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-519">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9f7ad-520">Zmienne środowiskowe poprzedzone znakiem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-520">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9f7ad-521">Prefiks ( `ASPNETCORE_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-521">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9f7ad-522">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-522">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9f7ad-523">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-523">App configuration is provided from:</span></span>
  * <span data-ttu-id="9f7ad-524">*appsettings.js* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-524">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9f7ad-525">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-525">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9f7ad-526">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-526">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9f7ad-527">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-527">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9f7ad-528">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-528">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="9f7ad-529">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-529">Security</span></span>

<span data-ttu-id="9f7ad-530">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-530">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="9f7ad-531">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-531">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9f7ad-532">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-532">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9f7ad-533">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-533">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9f7ad-534">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-534">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9f7ad-535"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-535"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9f7ad-536">Menedżer wpisów tajnych używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-536">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9f7ad-537">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-537">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9f7ad-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9f7ad-539">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-539">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9f7ad-540">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-540">Hierarchical configuration data</span></span>

<span data-ttu-id="9f7ad-541">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-541">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9f7ad-542">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-542">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="9f7ad-543">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-543">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9f7ad-544">Sekcje i klucze są spłaszczone przy użyciu dwukropka (), `:` Aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-544">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9f7ad-545">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-545">section0:key0</span></span>
* <span data-ttu-id="9f7ad-546">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-546">section0:key1</span></span>
* <span data-ttu-id="9f7ad-547">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-547">section1:key0</span></span>
* <span data-ttu-id="9f7ad-548">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-548">section1:key1</span></span>

<span data-ttu-id="9f7ad-549"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-549"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9f7ad-550">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-550">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="9f7ad-551">Konwencje</span><span class="sxs-lookup"><span data-stu-id="9f7ad-551">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="9f7ad-552">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-552">Sources and providers</span></span>

<span data-ttu-id="9f7ad-553">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-553">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9f7ad-554">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-554">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9f7ad-555">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-555">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9f7ad-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9f7ad-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> można wstrzyknąć do Razor stron <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC, <xref:Microsoft.AspNetCore.Mvc.Controller> Aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="9f7ad-558">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-558">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="9f7ad-559">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-559">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="9f7ad-560">Klucze</span><span class="sxs-lookup"><span data-stu-id="9f7ad-560">Keys</span></span>

<span data-ttu-id="9f7ad-561">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-561">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9f7ad-562">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-562">Keys are case-insensitive.</span></span> <span data-ttu-id="9f7ad-563">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-563">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9f7ad-564">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-564">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="9f7ad-565">Aby uzyskać więcej informacji na temat zduplikowanych kluczy JSON, zobacz [ten problem](https://github.com/dotnet/extensions/issues/2381)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-565">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="9f7ad-566">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="9f7ad-566">Hierarchical keys</span></span>
  * <span data-ttu-id="9f7ad-567">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-567">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9f7ad-568">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-568">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9f7ad-569">Podwójne podkreślenie ( `__` ) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-569">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="9f7ad-570">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-570">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9f7ad-571">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-571">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9f7ad-572"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-572">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9f7ad-573">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-573">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="9f7ad-574">Wartości</span><span class="sxs-lookup"><span data-stu-id="9f7ad-574">Values</span></span>

<span data-ttu-id="9f7ad-575">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-575">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9f7ad-576">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-576">Values are strings.</span></span>
* <span data-ttu-id="9f7ad-577">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-577">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9f7ad-578">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-578">Providers</span></span>

<span data-ttu-id="9f7ad-579">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-579">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9f7ad-580">Dostawca</span><span class="sxs-lookup"><span data-stu-id="9f7ad-580">Provider</span></span> | <span data-ttu-id="9f7ad-581">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="9f7ad-581">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="9f7ad-582">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące*zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="9f7ad-582">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="9f7ad-583">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f7ad-583">Azure Key Vault</span></span> |
| <span data-ttu-id="9f7ad-584">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="9f7ad-584">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="9f7ad-585">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9f7ad-585">Azure App Configuration</span></span> |
| [<span data-ttu-id="9f7ad-586">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-586">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="9f7ad-587">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-587">Command-line parameters</span></span> |
| [<span data-ttu-id="9f7ad-588">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-588">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="9f7ad-589">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-589">Custom source</span></span> |
| [<span data-ttu-id="9f7ad-590">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-590">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="9f7ad-591">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-591">Environment variables</span></span> |
| [<span data-ttu-id="9f7ad-592">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-592">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="9f7ad-593">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="9f7ad-593">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="9f7ad-594">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-594">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="9f7ad-595">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="9f7ad-595">Directory files</span></span> |
| [<span data-ttu-id="9f7ad-596">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="9f7ad-596">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="9f7ad-597">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="9f7ad-597">In-memory collections</span></span> |
| <span data-ttu-id="9f7ad-598">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tematy dotyczące*zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="9f7ad-598">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="9f7ad-599">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="9f7ad-599">File in the user profile directory</span></span> |

<span data-ttu-id="9f7ad-600">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-600">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9f7ad-601">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-601">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="9f7ad-602">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-602">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9f7ad-603">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-603">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9f7ad-604">Pliki (*appsettings.js*, *appSettings. { Environment}. JSON*, gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="9f7ad-604">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9f7ad-605">Usługa Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9f7ad-605">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9f7ad-606">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="9f7ad-606">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="9f7ad-607">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="9f7ad-607">Environment variables</span></span>
1. <span data-ttu-id="9f7ad-608">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-608">Command-line arguments</span></span>

<span data-ttu-id="9f7ad-609">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-609">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9f7ad-610">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu programu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-610">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f7ad-611">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-611">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="9f7ad-612">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="9f7ad-612">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="9f7ad-613">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-613">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="9f7ad-614">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9f7ad-614">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9f7ad-615">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-615">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="9f7ad-616">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-616">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="9f7ad-617">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-617">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="9f7ad-618">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="9f7ad-618">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="9f7ad-619">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder` , najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-619">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="9f7ad-620">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-620">Consume configuration during app startup</span></span>

<span data-ttu-id="9f7ad-621">Konfiguracja dostarczona do aplikacji w programie `ConfigureAppConfiguration` jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-621">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f7ad-622">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-622">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9f7ad-623">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-623">Command-line Configuration Provider</span></span>

<span data-ttu-id="9f7ad-624"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-624">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9f7ad-625">Aby uaktywnić konfigurację wiersza polecenia, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-625">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9f7ad-626">`AddCommandLine` jest wywoływana automatycznie, gdy `CreateDefaultBuilder(string [])` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-626">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="9f7ad-627">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-627">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9f7ad-628">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-628">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9f7ad-629">Opcjonalna konfiguracja *appsettings.jsna* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-629">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9f7ad-630">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-630">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9f7ad-631">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-631">Environment variables.</span></span>

<span data-ttu-id="9f7ad-632">`CreateDefaultBuilder` dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-632">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9f7ad-633">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-633">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9f7ad-634">`CreateDefaultBuilder` działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-634">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9f7ad-635">W związku z tym konfiguracja wiersza polecenia aktywowana przez program `CreateDefaultBuilder` może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-635">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9f7ad-636">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-636">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f7ad-637">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj jako `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-637">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="9f7ad-638">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-638">**Example**</span></span>

<span data-ttu-id="9f7ad-639">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-639">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9f7ad-640">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-640">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9f7ad-641">Podaj do polecenia argument wiersza polecenia `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-641">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9f7ad-642">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-642">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9f7ad-643">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-643">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9f7ad-644">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9f7ad-644">Arguments</span></span>

<span data-ttu-id="9f7ad-645">Wartość musi następować po znaku równości ( `=` ) lub klucz musi mieć prefiks ( `--` lub `/` ), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-645">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9f7ad-646">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-646">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9f7ad-647">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="9f7ad-647">Key prefix</span></span>               | <span data-ttu-id="9f7ad-648">Przykład</span><span class="sxs-lookup"><span data-stu-id="9f7ad-648">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="9f7ad-649">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="9f7ad-649">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="9f7ad-650">Dwie kreski ( `--` )</span><span class="sxs-lookup"><span data-stu-id="9f7ad-650">Two dashes (`--`)</span></span>        | <span data-ttu-id="9f7ad-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="9f7ad-652">Ukośnik ( `/` )</span><span class="sxs-lookup"><span data-stu-id="9f7ad-652">Forward slash (`/`)</span></span>      | <span data-ttu-id="9f7ad-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="9f7ad-654">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-654">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9f7ad-655">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-655">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9f7ad-656">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="9f7ad-656">Switch mappings</span></span>

<span data-ttu-id="9f7ad-657">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-657">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9f7ad-658">Podczas ręcznego kompilowania konfiguracji za pomocą programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-658">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9f7ad-659">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-659">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9f7ad-660">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-660">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9f7ad-661">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-661">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9f7ad-662">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-662">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9f7ad-663">Przełączniki muszą zaczynać się kreską ( `-` ) lub podwójną kreską ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-663">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9f7ad-664">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-664">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9f7ad-665">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-665">Create a switch mappings dictionary.</span></span> <span data-ttu-id="9f7ad-666">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-666">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="9f7ad-667">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-667">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="9f7ad-668">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-668">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9f7ad-669">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-669">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f7ad-670">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-670">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9f7ad-671">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-671">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9f7ad-672">Klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-672">Key</span></span>       | <span data-ttu-id="9f7ad-673">Wartość</span><span class="sxs-lookup"><span data-stu-id="9f7ad-673">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="9f7ad-674">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-674">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9f7ad-675">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-675">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9f7ad-676">Klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-676">Key</span></span>               | <span data-ttu-id="9f7ad-677">Wartość</span><span class="sxs-lookup"><span data-stu-id="9f7ad-677">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9f7ad-678">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="9f7ad-678">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9f7ad-679"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-679">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9f7ad-680">Aby uaktywnić konfigurację zmiennych środowiskowych, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-680">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9f7ad-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9f7ad-682">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-682">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9f7ad-683">`AddEnvironmentVariables` służy do ładowania zmiennych środowiskowych, które są poprzedzone `ASPNETCORE_` [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-683">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9f7ad-684">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-684">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9f7ad-685">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-685">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9f7ad-686">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez wywołanie `AddEnvironmentVariables` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-686">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9f7ad-687">Opcjonalna konfiguracja *appsettings.jsna* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-687">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9f7ad-688">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-688">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9f7ad-689">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-689">Command-line arguments.</span></span>

<span data-ttu-id="9f7ad-690">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-690">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9f7ad-691">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-691">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9f7ad-692">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddEnvironmentVariables` z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-692">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="9f7ad-693">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-693">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="9f7ad-694">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-694">**Example**</span></span>

<span data-ttu-id="9f7ad-695">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-695">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9f7ad-696">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-696">Run the sample app.</span></span> <span data-ttu-id="9f7ad-697">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-697">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9f7ad-698">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej środowiskowej `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-698">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9f7ad-699">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj w `Development` przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-699">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9f7ad-700">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-700">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="9f7ad-701">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-701">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="9f7ad-702">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić `FilteredConfiguration` stronę na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-702">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9f7ad-703">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-703">Prefixes</span></span>

<span data-ttu-id="9f7ad-704">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-704">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9f7ad-705">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_` , podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-705">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9f7ad-706">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-706">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9f7ad-707">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-707">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="9f7ad-708">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-708">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9f7ad-709">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-709">**Connection string prefixes**</span></span>

<span data-ttu-id="9f7ad-710">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-710">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9f7ad-711">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-711">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9f7ad-712">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="9f7ad-712">Connection string prefix</span></span> | <span data-ttu-id="9f7ad-713">Dostawca</span><span class="sxs-lookup"><span data-stu-id="9f7ad-713">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="9f7ad-714">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-714">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="9f7ad-715">MySQL</span><span class="sxs-lookup"><span data-stu-id="9f7ad-715">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="9f7ad-716">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="9f7ad-716">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="9f7ad-717">SQL Server</span><span class="sxs-lookup"><span data-stu-id="9f7ad-717">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="9f7ad-718">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-718">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9f7ad-719">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-719">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9f7ad-720">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-720">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9f7ad-721">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="9f7ad-721">Environment variable key</span></span> | <span data-ttu-id="9f7ad-722">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-722">Converted configuration key</span></span> | <span data-ttu-id="9f7ad-723">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="9f7ad-723">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-724">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-724">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-725">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9f7ad-726">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-726">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-727">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9f7ad-728">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-728">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9f7ad-729">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9f7ad-730">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-730">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="9f7ad-731">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-731">**Example**</span></span>

<span data-ttu-id="9f7ad-732">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-732">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="9f7ad-733">Nazwa: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-733">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="9f7ad-734">Wartość: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="9f7ad-734">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="9f7ad-735">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config` , odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-735">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="9f7ad-736">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-736">File Configuration Provider</span></span>

<span data-ttu-id="9f7ad-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9f7ad-738">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-738">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9f7ad-739">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="9f7ad-739">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9f7ad-740">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="9f7ad-740">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9f7ad-741">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="9f7ad-741">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9f7ad-742">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="9f7ad-742">INI Configuration Provider</span></span>

<span data-ttu-id="9f7ad-743"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-743">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9f7ad-744">Aby uaktywnić konfigurację pliku INI, wywołaj <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-744">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9f7ad-745">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-745">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9f7ad-746">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-746">Overloads permit specifying:</span></span>

* <span data-ttu-id="9f7ad-747">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-747">Whether the file is optional.</span></span>
* <span data-ttu-id="9f7ad-748">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-748">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9f7ad-749"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-749">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9f7ad-750">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-750">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9f7ad-751">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-751">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="9f7ad-752">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-752">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9f7ad-753">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-753">section0:key0</span></span>
* <span data-ttu-id="9f7ad-754">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-754">section0:key1</span></span>
* <span data-ttu-id="9f7ad-755">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="9f7ad-755">section1:subsection:key</span></span>
* <span data-ttu-id="9f7ad-756">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-756">section2:subsection0:key</span></span>
* <span data-ttu-id="9f7ad-757">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-757">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9f7ad-758">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="9f7ad-758">JSON Configuration Provider</span></span>

<span data-ttu-id="9f7ad-759"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-759">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9f7ad-760">Aby uaktywnić konfigurację pliku JSON, wywołaj <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-760">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9f7ad-761">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-761">Overloads permit specifying:</span></span>

* <span data-ttu-id="9f7ad-762">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-762">Whether the file is optional.</span></span>
* <span data-ttu-id="9f7ad-763">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-763">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9f7ad-764"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-764">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9f7ad-765">`AddJsonFile` jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-765">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f7ad-766">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-766">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9f7ad-767">*appsettings.js*: ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-767">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="9f7ad-768">Wersja środowiska pliku może przesłonić wartości podane przez *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-768">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9f7ad-769">*appSettings. {Environment}. JSON*: wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-769">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9f7ad-770">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-770">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9f7ad-771">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-771">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9f7ad-772">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-772">Environment variables.</span></span>
* <span data-ttu-id="9f7ad-773">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-773">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9f7ad-774">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-774">Command-line arguments.</span></span>

<span data-ttu-id="9f7ad-775">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-775">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9f7ad-776">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-776">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9f7ad-777">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *appsettings.jsw* i *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-777">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9f7ad-778">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-778">**Example**</span></span>

<span data-ttu-id="9f7ad-779">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-779">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="9f7ad-780">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.jsna*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-780">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9f7ad-781">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-781">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9f7ad-782">W przypadku *appsettings.Development.js* w aplikacji przykładowej załadowano następujący plik:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-782">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9f7ad-783">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-783">Run the sample app.</span></span> <span data-ttu-id="9f7ad-784">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-784">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9f7ad-785">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-785">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9f7ad-786">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-786">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9f7ad-787">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-787">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9f7ad-788">Otwórz *Właściwości/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-788">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9f7ad-789">W `ConfigurationSample` profilu Zmień wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej na `Production` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-789">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9f7ad-790">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-790">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9f7ad-791">Ustawienia w *appsettings.Development.js* nie przesłaniają już ustawień w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-791">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9f7ad-792">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-792">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="9f7ad-793">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="9f7ad-793">XML Configuration Provider</span></span>

<span data-ttu-id="9f7ad-794"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-794">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9f7ad-795">Aby uaktywnić konfigurację pliku XML, wywołaj <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-795">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9f7ad-796">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-796">Overloads permit specifying:</span></span>

* <span data-ttu-id="9f7ad-797">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-797">Whether the file is optional.</span></span>
* <span data-ttu-id="9f7ad-798">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-798">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9f7ad-799"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-799">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9f7ad-800">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-800">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9f7ad-801">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-801">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9f7ad-802">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-802">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9f7ad-803">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-803">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="9f7ad-804">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-804">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9f7ad-805">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-805">section0:key0</span></span>
* <span data-ttu-id="9f7ad-806">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-806">section0:key1</span></span>
* <span data-ttu-id="9f7ad-807">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-807">section1:key0</span></span>
* <span data-ttu-id="9f7ad-808">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-808">section1:key1</span></span>

<span data-ttu-id="9f7ad-809">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-809">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="9f7ad-810">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-810">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9f7ad-811">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-811">section:section0:key:key0</span></span>
* <span data-ttu-id="9f7ad-812">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-812">section:section0:key:key1</span></span>
* <span data-ttu-id="9f7ad-813">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-813">section:section1:key:key0</span></span>
* <span data-ttu-id="9f7ad-814">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-814">section:section1:key:key1</span></span>

<span data-ttu-id="9f7ad-815">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-815">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9f7ad-816">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="9f7ad-816">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9f7ad-817">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="9f7ad-817">key:attribute</span></span>
* <span data-ttu-id="9f7ad-818">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="9f7ad-818">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9f7ad-819">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="9f7ad-819">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9f7ad-820"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-820">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9f7ad-821">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-821">The key is the file name.</span></span> <span data-ttu-id="9f7ad-822">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-822">The value contains the file's contents.</span></span> <span data-ttu-id="9f7ad-823">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-823">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9f7ad-824">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-824">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9f7ad-825">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-825">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9f7ad-826">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-826">Overloads permit specifying:</span></span>

* <span data-ttu-id="9f7ad-827">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-827">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9f7ad-828">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-828">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9f7ad-829">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-829">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9f7ad-830">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-830">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9f7ad-831">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-831">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9f7ad-832">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="9f7ad-832">Memory Configuration Provider</span></span>

<span data-ttu-id="9f7ad-833"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-833">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9f7ad-834">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-834">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9f7ad-835">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-835">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9f7ad-836">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-836">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9f7ad-837">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-837">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="9f7ad-838">Słownik jest używany z wywołaniem do `AddInMemoryCollection` zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-838">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="9f7ad-839">GetValue</span><span class="sxs-lookup"><span data-stu-id="9f7ad-839">GetValue</span></span>

<span data-ttu-id="9f7ad-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="9f7ad-841">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-841">An overload accepts a default value.</span></span>

<span data-ttu-id="9f7ad-842">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-842">The following example:</span></span>

* <span data-ttu-id="9f7ad-843">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-843">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9f7ad-844">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-844">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9f7ad-845">Typ wartości `int` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-845">Types the value as an `int`.</span></span>
* <span data-ttu-id="9f7ad-846">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-846">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9f7ad-847">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="9f7ad-847">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9f7ad-848">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-848">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9f7ad-849">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-849">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="9f7ad-850">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-850">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9f7ad-851">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-851">section0:key0</span></span>
* <span data-ttu-id="9f7ad-852">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-852">section0:key1</span></span>
* <span data-ttu-id="9f7ad-853">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-853">section1:key0</span></span>
* <span data-ttu-id="9f7ad-854">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-854">section1:key1</span></span>
* <span data-ttu-id="9f7ad-855">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-855">section2:subsection0:key0</span></span>
* <span data-ttu-id="9f7ad-856">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-856">section2:subsection0:key1</span></span>
* <span data-ttu-id="9f7ad-857">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-857">section2:subsection1:key0</span></span>
* <span data-ttu-id="9f7ad-858">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-858">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9f7ad-859">GetSection</span><span class="sxs-lookup"><span data-stu-id="9f7ad-859">GetSection</span></span>

<span data-ttu-id="9f7ad-860">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-860">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9f7ad-861">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1` , wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-861">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9f7ad-862">`configSection`Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-862">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9f7ad-863">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0` , wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-863">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9f7ad-864">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-864">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9f7ad-865">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-865">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9f7ad-866">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-866">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9f7ad-867">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-867">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9f7ad-868">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="9f7ad-868">GetChildren</span></span>

<span data-ttu-id="9f7ad-869">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w programie `section2` uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-869">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9f7ad-870">Exists</span><span class="sxs-lookup"><span data-stu-id="9f7ad-870">Exists</span></span>

<span data-ttu-id="9f7ad-871">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-871">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9f7ad-872">Dane przykładowe są spowodowane tym, że `sectionExists` `false` `section2:subsection2` w danych konfiguracji nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-872">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9f7ad-873">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="9f7ad-873">Bind to an object graph</span></span>

<span data-ttu-id="9f7ad-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9f7ad-875">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-875">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="9f7ad-876">Przykład zawiera `TvShow` model, którego obiekt zawiera obiekty `Metadata` i `Actors` klasy (*modele/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="9f7ad-876">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="9f7ad-877">Przykładowa aplikacja zawiera plik *tvshow.xml* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-877">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="9f7ad-878">Konfiguracja jest powiązana z `TvShow` wykresem całego obiektu za pomocą `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-878">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9f7ad-879">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-879">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9f7ad-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9f7ad-881">`Get<T>` jest wygodniejszy niż używanie `Bind` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-881">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9f7ad-882">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-882">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9f7ad-883">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="9f7ad-883">Bind an array to a class</span></span>

<span data-ttu-id="9f7ad-884">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="9f7ad-884">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9f7ad-885"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-885">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9f7ad-886">Każdy format tablicy, który ujawnia segment klucza numerycznego ( `:0:` , `:1:` , &hellip; `:{n}:` ) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-886">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="9f7ad-887">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-887">Binding is provided by convention.</span></span> <span data-ttu-id="9f7ad-888">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-888">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9f7ad-889">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-889">**In-memory array processing**</span></span>

<span data-ttu-id="9f7ad-890">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-890">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9f7ad-891">Klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-891">Key</span></span>             | <span data-ttu-id="9f7ad-892">Wartość</span><span class="sxs-lookup"><span data-stu-id="9f7ad-892">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9f7ad-893">Tablica: wpisy: 0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-893">array:entries:0</span></span> | <span data-ttu-id="9f7ad-894">value0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-894">value0</span></span> |
| <span data-ttu-id="9f7ad-895">Tablica: wpisy: 1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-895">array:entries:1</span></span> | <span data-ttu-id="9f7ad-896">sekwencj</span><span class="sxs-lookup"><span data-stu-id="9f7ad-896">value1</span></span> |
| <span data-ttu-id="9f7ad-897">Tablica: wpisy: 2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-897">array:entries:2</span></span> | <span data-ttu-id="9f7ad-898">wartość2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-898">value2</span></span> |
| <span data-ttu-id="9f7ad-899">Tablica: wpisy: 4</span><span class="sxs-lookup"><span data-stu-id="9f7ad-899">array:entries:4</span></span> | <span data-ttu-id="9f7ad-900">value4</span><span class="sxs-lookup"><span data-stu-id="9f7ad-900">value4</span></span> |
| <span data-ttu-id="9f7ad-901">Tablica: wpisy: 5</span><span class="sxs-lookup"><span data-stu-id="9f7ad-901">array:entries:5</span></span> | <span data-ttu-id="9f7ad-902">value5</span><span class="sxs-lookup"><span data-stu-id="9f7ad-902">value5</span></span> |

<span data-ttu-id="9f7ad-903">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-903">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="9f7ad-904">Tablica pomija wartość dla indeksu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-904">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9f7ad-905">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9f7ad-906">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-906">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="9f7ad-907">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-907">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9f7ad-908">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-908">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="9f7ad-909">Obiekt powiązany, wystąpienie elementu `ArrayExample` , otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-909">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9f7ad-910">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="9f7ad-910">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9f7ad-911">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="9f7ad-911">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9f7ad-912">0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-912">0</span></span>                            | <span data-ttu-id="9f7ad-913">value0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-913">value0</span></span>                       |
| <span data-ttu-id="9f7ad-914">1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-914">1</span></span>                            | <span data-ttu-id="9f7ad-915">sekwencj</span><span class="sxs-lookup"><span data-stu-id="9f7ad-915">value1</span></span>                       |
| <span data-ttu-id="9f7ad-916">2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-916">2</span></span>                            | <span data-ttu-id="9f7ad-917">wartość2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-917">value2</span></span>                       |
| <span data-ttu-id="9f7ad-918">3</span><span class="sxs-lookup"><span data-stu-id="9f7ad-918">3</span></span>                            | <span data-ttu-id="9f7ad-919">value4</span><span class="sxs-lookup"><span data-stu-id="9f7ad-919">value4</span></span>                       |
| <span data-ttu-id="9f7ad-920">4</span><span class="sxs-lookup"><span data-stu-id="9f7ad-920">4</span></span>                            | <span data-ttu-id="9f7ad-921">value5</span><span class="sxs-lookup"><span data-stu-id="9f7ad-921">value5</span></span>                       |

<span data-ttu-id="9f7ad-922">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-922">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9f7ad-923">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-923">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9f7ad-924">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-924">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9f7ad-925">Brakujący element konfiguracji dla indeksu &num; 3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-925">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9f7ad-926">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-926">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9f7ad-927">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-927">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9f7ad-928">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-928">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9f7ad-929">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-929">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9f7ad-930">Klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-930">Key</span></span>             | <span data-ttu-id="9f7ad-931">Wartość</span><span class="sxs-lookup"><span data-stu-id="9f7ad-931">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9f7ad-932">Tablica: wpisy: 3</span><span class="sxs-lookup"><span data-stu-id="9f7ad-932">array:entries:3</span></span> | <span data-ttu-id="9f7ad-933">Wartość3</span><span class="sxs-lookup"><span data-stu-id="9f7ad-933">value3</span></span> |

<span data-ttu-id="9f7ad-934">Jeśli `ArrayExample` wystąpienie klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num; 3, `ArrayExample.Entries` Tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-934">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9f7ad-935">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="9f7ad-935">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9f7ad-936">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="9f7ad-936">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9f7ad-937">0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-937">0</span></span>                            | <span data-ttu-id="9f7ad-938">value0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-938">value0</span></span>                       |
| <span data-ttu-id="9f7ad-939">1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-939">1</span></span>                            | <span data-ttu-id="9f7ad-940">sekwencj</span><span class="sxs-lookup"><span data-stu-id="9f7ad-940">value1</span></span>                       |
| <span data-ttu-id="9f7ad-941">2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-941">2</span></span>                            | <span data-ttu-id="9f7ad-942">wartość2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-942">value2</span></span>                       |
| <span data-ttu-id="9f7ad-943">3</span><span class="sxs-lookup"><span data-stu-id="9f7ad-943">3</span></span>                            | <span data-ttu-id="9f7ad-944">Wartość3</span><span class="sxs-lookup"><span data-stu-id="9f7ad-944">value3</span></span>                       |
| <span data-ttu-id="9f7ad-945">4</span><span class="sxs-lookup"><span data-stu-id="9f7ad-945">4</span></span>                            | <span data-ttu-id="9f7ad-946">value4</span><span class="sxs-lookup"><span data-stu-id="9f7ad-946">value4</span></span>                       |
| <span data-ttu-id="9f7ad-947">5</span><span class="sxs-lookup"><span data-stu-id="9f7ad-947">5</span></span>                            | <span data-ttu-id="9f7ad-948">value5</span><span class="sxs-lookup"><span data-stu-id="9f7ad-948">value5</span></span>                       |

<span data-ttu-id="9f7ad-949">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="9f7ad-949">**JSON array processing**</span></span>

<span data-ttu-id="9f7ad-950">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-950">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9f7ad-951">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-951">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="9f7ad-952">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-952">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9f7ad-953">Klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-953">Key</span></span>                     | <span data-ttu-id="9f7ad-954">Wartość</span><span class="sxs-lookup"><span data-stu-id="9f7ad-954">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="9f7ad-955">json_array: klucz</span><span class="sxs-lookup"><span data-stu-id="9f7ad-955">json_array:key</span></span>          | <span data-ttu-id="9f7ad-956">wartośća</span><span class="sxs-lookup"><span data-stu-id="9f7ad-956">valueA</span></span> |
| <span data-ttu-id="9f7ad-957">json_array: podsekcja: 0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-957">json_array:subsection:0</span></span> | <span data-ttu-id="9f7ad-958">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="9f7ad-958">valueB</span></span> |
| <span data-ttu-id="9f7ad-959">json_array: podsekcja: 1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-959">json_array:subsection:1</span></span> | <span data-ttu-id="9f7ad-960">valueC</span><span class="sxs-lookup"><span data-stu-id="9f7ad-960">valueC</span></span> |
| <span data-ttu-id="9f7ad-961">json_array: podsekcja: 2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-961">json_array:subsection:2</span></span> | <span data-ttu-id="9f7ad-962">Znajdując</span><span class="sxs-lookup"><span data-stu-id="9f7ad-962">valueD</span></span> |

<span data-ttu-id="9f7ad-963">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-963">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="9f7ad-964">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-964">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9f7ad-965">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-965">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9f7ad-966">`JsonArrayExample.Subsection` Indeks</span><span class="sxs-lookup"><span data-stu-id="9f7ad-966">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9f7ad-967">`JsonArrayExample.Subsection` Wartościami</span><span class="sxs-lookup"><span data-stu-id="9f7ad-967">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="9f7ad-968">0</span><span class="sxs-lookup"><span data-stu-id="9f7ad-968">0</span></span>                                   | <span data-ttu-id="9f7ad-969">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="9f7ad-969">valueB</span></span>                              |
| <span data-ttu-id="9f7ad-970">1</span><span class="sxs-lookup"><span data-stu-id="9f7ad-970">1</span></span>                                   | <span data-ttu-id="9f7ad-971">valueC</span><span class="sxs-lookup"><span data-stu-id="9f7ad-971">valueC</span></span>                              |
| <span data-ttu-id="9f7ad-972">2</span><span class="sxs-lookup"><span data-stu-id="9f7ad-972">2</span></span>                                   | <span data-ttu-id="9f7ad-973">Znajdując</span><span class="sxs-lookup"><span data-stu-id="9f7ad-973">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="9f7ad-974">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9f7ad-974">Custom configuration provider</span></span>

<span data-ttu-id="9f7ad-975">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-975">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9f7ad-976">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-976">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9f7ad-977">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-977">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9f7ad-978">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-978">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9f7ad-979">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-979">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9f7ad-980">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-980">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9f7ad-981">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-981">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9f7ad-982">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-982">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9f7ad-983">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-983">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9f7ad-984">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-984">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9f7ad-985">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-985">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9f7ad-986">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-986">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9f7ad-987">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-987">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9f7ad-988">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-988">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9f7ad-989">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-989">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="9f7ad-990">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-990">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9f7ad-991">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-991">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9f7ad-992">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-992">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9f7ad-993">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-993">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9f7ad-994">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="9f7ad-994">Access configuration during startup</span></span>

<span data-ttu-id="9f7ad-995">Wsuń `IConfiguration` do `Startup` konstruktora, aby uzyskać dostęp do wartości konfiguracyjnych w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9f7ad-995">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f7ad-996">Aby uzyskać dostęp do konfiguracji w programie `Startup.Configure` , należy wstrzyknąć `IConfiguration` bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-996">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="9f7ad-997">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="9f7ad-997">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="9f7ad-998">Konfiguracja dostępu na Razor stronie stron lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="9f7ad-998">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9f7ad-999">Aby uzyskać dostęp do ustawień konfiguracji na Razor stronie stron lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [ przestrzeni nazwMicrosoft.Extensions.Configwersja](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-999">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9f7ad-1000">Na Razor stronie stron:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-1000">In a Razor Pages page:</span></span>

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

<span data-ttu-id="9f7ad-1001">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="9f7ad-1001">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9f7ad-1002">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="9f7ad-1002">Add configuration from an external assembly</span></span>

<span data-ttu-id="9f7ad-1003"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-1003">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9f7ad-1004">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f7ad-1004">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f7ad-1005">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9f7ad-1005">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end