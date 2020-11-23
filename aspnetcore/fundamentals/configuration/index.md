---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core przy użyciu interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: c04dcc65f7518d2d8b32cdce7a7fbb756dd8ec3a
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417542"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8f6f1-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f6f1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8f6f1-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8f6f1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8f6f1-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8f6f1-106">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8f6f1-107">Pliki ustawień, takie jak *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8f6f1-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8f6f1-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-108">Environment variables</span></span>
* <span data-ttu-id="8f6f1-109">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8f6f1-109">Azure Key Vault</span></span>
* <span data-ttu-id="8f6f1-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8f6f1-110">Azure App Configuration</span></span>
* <span data-ttu-id="8f6f1-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-111">Command-line arguments</span></span>
* <span data-ttu-id="8f6f1-112">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="8f6f1-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="8f6f1-113">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="8f6f1-113">Directory files</span></span>
* <span data-ttu-id="8f6f1-114">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="8f6f1-114">In-memory .NET objects</span></span>

<span data-ttu-id="8f6f1-115">Ten temat zawiera informacje dotyczące konfiguracji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="8f6f1-116">Aby uzyskać informacje na temat korzystania z konfiguracji w aplikacjach konsolowych, zobacz [Konfiguracja platformy .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="8f6f1-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8f6f1-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8f6f1-118">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="8f6f1-118">Default configuration</span></span>

<span data-ttu-id="8f6f1-119">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8f6f1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8f6f1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący element `IConfiguration` jako źródło.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8f6f1-122">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8f6f1-123">[appsettings.json](#appsettingsjson) Korzystanie z [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8f6f1-124">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8f6f1-125">Na przykład *AppSettings*. ***Produkcja \* \* _._json* i *AppSettings*. \* \* \* programowanie** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="8f6f1-126">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8f6f1-127">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8f6f1-128">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8f6f1-129">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8f6f1-130">Na przykład jeśli `MyKey` jest ustawiona w obu *appsettings.json* i środowisku, wartość środowiska jest używana.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8f6f1-131">Przy użyciu domyślnych dostawców konfiguracji  [dostawca konfiguracji wiersza polecenia](#clcp) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="8f6f1-132">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8f6f1-133">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="8f6f1-134">Weź pod uwagę następujący *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8f6f1-135">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-136">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="8f6f1-137">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings*. ***Produkcja \* \* _._json* i *AppSettings*. \* \* \* pliki deweloperskie** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="8f6f1-138">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8f6f1-139">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8f6f1-140">*AppSettings*. `Environment` . wartości *JSON* przesłaniają klucze w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8f6f1-141">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-141">For example, by default:</span></span>

* <span data-ttu-id="8f6f1-142">W programowaniu, *AppSettings*. \***Development** _._json \* konfiguracja zastępuje wartości Znalezione w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8f6f1-143">W środowisku produkcyjnym *AppSettings*. \***Production** _._json \* konfiguracja zastępuje wartości Znalezione w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8f6f1-144">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8f6f1-145">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="8f6f1-146">Przy użyciu konfiguracji [domyślnej](#default) , *appsettings.json* a *appSettings.* `Environment` pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8f6f1-147">Zmiany wprowadzone w *appsettings.json* i *appSettings.* `Environment` plik *. JSON* \***po** _ uruchomienia aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8f6f1-148">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="8f6f1-149">Łączenie kolekcji usług</span><span class="sxs-lookup"><span data-stu-id="8f6f1-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="8f6f1-150">Security and Secret Manager</span><span class="sxs-lookup"><span data-stu-id="8f6f1-150">Security and secret manager</span></span>

<span data-ttu-id="8f6f1-151">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-151">Configuration data guidelines:</span></span>

<span data-ttu-id="8f6f1-152">Nigdy nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8f6f1-153">Za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="8f6f1-154">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8f6f1-155">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8f6f1-156">[Domyślnie](#default)program [Secret Manager](xref:security/app-secrets) odczytuje ustawienia konfiguracji po *appsettings.json* i *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8f6f1-157">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8f6f1-158"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8f6f1-159">Menedżer wpisów tajnych używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8f6f1-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8f6f1-161">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8f6f1-162">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-162">Environment variables</span></span>

<span data-ttu-id="8f6f1-163">Przy użyciu konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> ładowana konfiguracja ze zmiennej środowiskowej par klucz-wartość po odczytu *appsettings.json* , *appSettings.* `Environment` *. JSON* i [Menedżer wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8f6f1-164">W związku z tym kluczowe wartości są odczytywane z wartości zastąpienia środowiska odczytywane z *appsettings.json* , *appSettings.* `Environment` *. JSON* i Menedżer wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8f6f1-165">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-165">The following `set` commands:</span></span>

* <span data-ttu-id="8f6f1-166">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8f6f1-167">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8f6f1-168">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8f6f1-169">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-169">The preceding environment settings:</span></span>

* <span data-ttu-id="8f6f1-170">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8f6f1-171">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8f6f1-172">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8f6f1-173">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8f6f1-174">`/M` ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8f6f1-175">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8f6f1-176">Aby sprawdzić, czy poprzednie polecenia zastępują *appsettings.json* i *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8f6f1-177">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8f6f1-178">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i wprowadź `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8f6f1-179">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8f6f1-180">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-180">In the preceding code:</span></span>

* <span data-ttu-id="8f6f1-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8f6f1-182">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8f6f1-183">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8f6f1-184">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8f6f1-185">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8f6f1-186">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8f6f1-187">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8f6f1-188">`DOTNET_` `ASPNETCORE_` Prefiksy i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8f6f1-189">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8f6f1-190">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8f6f1-191">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8f6f1-192">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8f6f1-193">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-193">Exposed as environment variables.</span></span>

<span data-ttu-id="8f6f1-194">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8f6f1-195">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="8f6f1-196">Nazewnictwo zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-196">Naming of environment variables</span></span>

<span data-ttu-id="8f6f1-197">Nazwy zmiennych środowiskowych odzwierciedlają strukturę *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-197">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="8f6f1-198">Każdy element w hierarchii jest oddzielony znakiem podwójnego podkreślenia (preferowany) lub dwukropek.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-198">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="8f6f1-199">Gdy struktura elementu zawiera tablicę, indeks tablicy powinien być traktowany jako dodatkowa nazwa elementu w tej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-199">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="8f6f1-200">Rozważmy następujący *appsettings.json* plik i jego równoważne wartości reprezentowane jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-200">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="8f6f1-201">**zmienne środowiskowe**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-201">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="8f6f1-202">Zmienne środowiskowe ustawione w launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="8f6f1-202">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="8f6f1-203">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-203">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8f6f1-204">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-204">Command-line</span></span>

<span data-ttu-id="8f6f1-205">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-205">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8f6f1-206">*appsettings.json* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-206">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8f6f1-207">Wpisy [tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-207">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8f6f1-208">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-208">Environment variables.</span></span>

<span data-ttu-id="8f6f1-209">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-209">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8f6f1-210">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-210">Command-line arguments</span></span>

<span data-ttu-id="8f6f1-211">Następujące polecenie ustawia klucze i wartości przy użyciu `=` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-211">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8f6f1-212">Następujące polecenie ustawia klucze i wartości przy użyciu `/` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-212">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8f6f1-213">Następujące polecenie ustawia klucze i wartości przy użyciu `--` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-213">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8f6f1-214">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-214">The key value:</span></span>

* <span data-ttu-id="8f6f1-215">Musi `=` być zgodna lub musi mieć prefiks lub, gdy wartość znajduje się w `--` `/` miejscu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-215">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8f6f1-216">Nie jest wymagane, jeśli `=` jest używany.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-216">Isn't required if `=` is used.</span></span> <span data-ttu-id="8f6f1-217">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-217">For example, `MySetting=`.</span></span>

<span data-ttu-id="8f6f1-218">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które są używane `=` z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-218">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8f6f1-219">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="8f6f1-219">Switch mappings</span></span>

<span data-ttu-id="8f6f1-220">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-220">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8f6f1-221">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-221">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8f6f1-222">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-222">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8f6f1-223">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-223">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8f6f1-224">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-224">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8f6f1-225">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-225">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8f6f1-226">Przełączniki muszą zaczynać się od `-` lub `--` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-226">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8f6f1-227">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-227">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8f6f1-228">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-228">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8f6f1-229">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-229">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-230">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-230">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8f6f1-231">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-231">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8f6f1-232">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-232">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8f6f1-233">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-233">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8f6f1-234">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-234">Hierarchical configuration data</span></span>

<span data-ttu-id="8f6f1-235">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-235">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8f6f1-236">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-236">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8f6f1-237">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-237">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-238">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-238">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8f6f1-239">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-239">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8f6f1-240"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-240"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8f6f1-241">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-241">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8f6f1-242">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-242">Configuration keys and values</span></span>

<span data-ttu-id="8f6f1-243">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-243">Configuration keys:</span></span>

* <span data-ttu-id="8f6f1-244">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-244">Are case-insensitive.</span></span> <span data-ttu-id="8f6f1-245">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-245">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8f6f1-246">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-246">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8f6f1-247">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-247">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8f6f1-248">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="8f6f1-248">Hierarchical keys</span></span>
  * <span data-ttu-id="8f6f1-249">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-249">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8f6f1-250">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-250">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8f6f1-251">Podwójne podkreślenie, `__` ,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-251">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8f6f1-252">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-252">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8f6f1-253">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-253">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8f6f1-254"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-254">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8f6f1-255">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-255">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8f6f1-256">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-256">Configuration values:</span></span>

* <span data-ttu-id="8f6f1-257">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-257">Are strings.</span></span>
* <span data-ttu-id="8f6f1-258">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-258">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8f6f1-259">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-259">Configuration providers</span></span>

<span data-ttu-id="8f6f1-260">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-260">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8f6f1-261">Dostawca</span><span class="sxs-lookup"><span data-stu-id="8f6f1-261">Provider</span></span> | <span data-ttu-id="8f6f1-262">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="8f6f1-262">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="8f6f1-263">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8f6f1-263">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="8f6f1-264">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8f6f1-264">Azure Key Vault</span></span> |
| [<span data-ttu-id="8f6f1-265">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="8f6f1-265">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="8f6f1-266">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8f6f1-266">Azure App Configuration</span></span> |
| [<span data-ttu-id="8f6f1-267">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-267">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="8f6f1-268">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-268">Command-line parameters</span></span> |
| [<span data-ttu-id="8f6f1-269">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-269">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8f6f1-270">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-270">Custom source</span></span> |
| [<span data-ttu-id="8f6f1-271">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-271">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="8f6f1-272">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-272">Environment variables</span></span> |
| [<span data-ttu-id="8f6f1-273">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-273">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8f6f1-274">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="8f6f1-274">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="8f6f1-275">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-275">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8f6f1-276">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="8f6f1-276">Directory files</span></span> |
| [<span data-ttu-id="8f6f1-277">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="8f6f1-277">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8f6f1-278">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="8f6f1-278">In-memory collections</span></span> |
| [<span data-ttu-id="8f6f1-279">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-279">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="8f6f1-280">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="8f6f1-280">File in the user profile directory</span></span> |

<span data-ttu-id="8f6f1-281">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-281">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8f6f1-282">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-282">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8f6f1-283">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-283">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="8f6f1-284">*AppSettings*. `Environment` . *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="8f6f1-284">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8f6f1-285">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-285">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8f6f1-286">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-286">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8f6f1-287">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-287">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8f6f1-288">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-288">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8f6f1-289">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-289">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8f6f1-290">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-290">Connection string prefixes</span></span>

<span data-ttu-id="8f6f1-291">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-291">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8f6f1-292">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-292">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8f6f1-293">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-293">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8f6f1-294">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-294">Connection string prefix</span></span> | <span data-ttu-id="8f6f1-295">Dostawca</span><span class="sxs-lookup"><span data-stu-id="8f6f1-295">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8f6f1-296">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-296">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8f6f1-297">MySQL</span><span class="sxs-lookup"><span data-stu-id="8f6f1-297">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8f6f1-298">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8f6f1-298">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8f6f1-299">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8f6f1-299">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8f6f1-300">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-300">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8f6f1-301">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-301">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8f6f1-302">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-302">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8f6f1-303">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="8f6f1-303">Environment variable key</span></span> | <span data-ttu-id="8f6f1-304">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-304">Converted configuration key</span></span> | <span data-ttu-id="8f6f1-305">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-305">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-306">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-306">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-307">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8f6f1-308">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-308">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-309">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-309">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8f6f1-310">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-310">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-311">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-311">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8f6f1-312">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-312">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8f6f1-313">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-313">File configuration provider</span></span>

<span data-ttu-id="8f6f1-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8f6f1-315">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-315">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8f6f1-316">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="8f6f1-316">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8f6f1-317">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="8f6f1-317">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8f6f1-318">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="8f6f1-318">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8f6f1-319">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="8f6f1-319">INI configuration provider</span></span>

<span data-ttu-id="8f6f1-320"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-320">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8f6f1-321">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8f6f1-322">W powyższym kodzie ustawienia w *MyIniConfig.ini* i  *MyIniConfig*. `Environment` . pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-322">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8f6f1-323">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-323">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8f6f1-324">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-324">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8f6f1-325">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8f6f1-326">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8f6f1-327">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="8f6f1-327">JSON configuration provider</span></span>

<span data-ttu-id="8f6f1-328"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-328">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8f6f1-329">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-329">Overloads can specify:</span></span>

* <span data-ttu-id="8f6f1-330">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-330">Whether the file is optional.</span></span>
* <span data-ttu-id="8f6f1-331">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-331">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8f6f1-332">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-332">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8f6f1-333">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-333">The preceding code:</span></span>

* <span data-ttu-id="8f6f1-334">Konfiguruje dostawcę konfiguracji JSON w celu załadowania *MyConfig.jsw* pliku z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-334">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8f6f1-335">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-335">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8f6f1-336">`reloadOnChange: true` : Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-336">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8f6f1-337">Odczytuje [domyślnych dostawców konfiguracji](#default) przed *MyConfig.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-337">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8f6f1-338">Ustawienia w *MyConfig.js* ustawienia przesłania pliku w domyślnych dostawcach konfiguracji, w tym [dostawca konfiguracji zmiennych środowiskowych](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-338">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8f6f1-339">Zazwyczaj \***nie** należy przeznaczać wartości niestandardowych plików JSON, które są zastępowane przez [dostawcę konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-339">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8f6f1-340">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-340">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8f6f1-341">W powyższym kodzie ustawienia w _MyConfig.jsna \* i dla  *konfiguracji*. `Environment` .. pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-341">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8f6f1-342">Zastąp ustawienia w *appsettings.json* i *AppSettings*. `Environment` .. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-342">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8f6f1-343">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-343">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8f6f1-344">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *MyConfig.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-344">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8f6f1-345">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-345">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8f6f1-346">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="8f6f1-346">XML configuration provider</span></span>

<span data-ttu-id="8f6f1-347"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-347">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8f6f1-348">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8f6f1-349">W powyższym kodzie ustawienia w *MyXMLFile.xml* i  *MyXMLFile*. `Environment` . pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-349">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8f6f1-350">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-350">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8f6f1-351">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-351">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8f6f1-352">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8f6f1-353">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-354">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-354">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8f6f1-355">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-355">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-356">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-356">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8f6f1-357">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-357">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8f6f1-358">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="8f6f1-358">key:attribute</span></span>
* <span data-ttu-id="8f6f1-359">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="8f6f1-359">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8f6f1-360">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-360">Key-per-file configuration provider</span></span>

<span data-ttu-id="8f6f1-361"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-361">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8f6f1-362">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-362">The key is the file name.</span></span> <span data-ttu-id="8f6f1-363">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-363">The value contains the file's contents.</span></span> <span data-ttu-id="8f6f1-364">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-364">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8f6f1-365">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-365">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8f6f1-366">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-366">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8f6f1-367">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-367">Overloads permit specifying:</span></span>

* <span data-ttu-id="8f6f1-368">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-368">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8f6f1-369">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-369">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8f6f1-370">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-370">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8f6f1-371">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-371">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8f6f1-372">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-372">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8f6f1-373">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="8f6f1-373">Memory configuration provider</span></span>

<span data-ttu-id="8f6f1-374"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-374">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8f6f1-375">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-375">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8f6f1-376">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-376">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-377">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-377">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8f6f1-378">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-378">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8f6f1-379">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-379">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="8f6f1-380">GetValue</span><span class="sxs-lookup"><span data-stu-id="8f6f1-380">GetValue</span></span>

<span data-ttu-id="8f6f1-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-382">W powyższym kodzie, jeśli `NumberKey` nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-382">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8f6f1-383">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="8f6f1-383">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8f6f1-384">W poniższych przykładach należy wziąć pod uwagę następujące *MySubsection.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-384">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8f6f1-385">Poniższy kod dodaje *MySubsection.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-385">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8f6f1-386">GetSection</span><span class="sxs-lookup"><span data-stu-id="8f6f1-386">GetSection</span></span>

<span data-ttu-id="8f6f1-387">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8f6f1-388">Poniższy kod zwraca wartości dla `section1` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-388">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-389">Poniższy kod zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-389">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-390">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-390">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8f6f1-391">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-391">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8f6f1-392">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-392">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8f6f1-393">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-393">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8f6f1-394">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="8f6f1-394">GetChildren and Exists</span></span>

<span data-ttu-id="8f6f1-395">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-395">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-396">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-396">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8f6f1-397">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="8f6f1-397">Bind an array</span></span>

<span data-ttu-id="8f6f1-398">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-398">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8f6f1-399">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-399">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8f6f1-400">Rozważ *MyArray.jsna* podstawie pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="8f6f1-400">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8f6f1-401">Poniższy kod dodaje *MyArray.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-401">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8f6f1-402">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-402">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-403">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8f6f1-404">W poprzednich danych wyjściowych indeks 3 ma wartość `value40` odpowiadającą wartości `"4": "value40",` w *MyArray.jsna*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-404">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8f6f1-405">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-405">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8f6f1-406">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-406">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8f6f1-407">Poniższy kod ładuje `array:entries` konfigurację z użyciem <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-407">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8f6f1-408">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-408">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-409">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-409">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8f6f1-410">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-410">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8f6f1-411">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-411">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8f6f1-412">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-412">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8f6f1-413">Brakujący element konfiguracji dla indeksu &num; 3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje &num; parę klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-413">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8f6f1-414">Rozważmy następujące *Value3.js* pliku z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-414">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8f6f1-415">Poniższy kod zawiera konfigurację dla *Value3.js* i `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-415">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8f6f1-416">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-416">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-417">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-417">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8f6f1-418">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-418">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8f6f1-419">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-419">Custom configuration provider</span></span>

<span data-ttu-id="8f6f1-420">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-420">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8f6f1-421">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-421">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8f6f1-422">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-422">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8f6f1-423">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-423">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8f6f1-424">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-424">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8f6f1-425">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-425">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8f6f1-426">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-426">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8f6f1-427">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-427">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8f6f1-428">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-428">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8f6f1-429">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-429">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8f6f1-430">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8f6f1-431">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-431">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8f6f1-432">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8f6f1-433">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-433">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8f6f1-434">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-434">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8f6f1-435">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-435">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8f6f1-436">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8f6f1-437">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-437">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8f6f1-438">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-438">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8f6f1-439">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-439">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8f6f1-440">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="8f6f1-440">Access configuration in Startup</span></span>

<span data-ttu-id="8f6f1-441">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-441">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8f6f1-442">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-442">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="8f6f1-443">Konfiguracja dostępu na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="8f6f1-443">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8f6f1-444">Poniższy kod przedstawia dane konfiguracji na Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-444">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="8f6f1-445">W poniższym kodzie `MyOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-445">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="8f6f1-446">Następujące oznakowanie używa [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy do rozwiązywania i wyświetlania wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-446">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8f6f1-447">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="8f6f1-447">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8f6f1-448">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-448">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="8f6f1-449">Konfigurowanie opcji za pomocą delegata</span><span class="sxs-lookup"><span data-stu-id="8f6f1-449">Configure options with a delegate</span></span>

<span data-ttu-id="8f6f1-450">Opcje skonfigurowane w wartościach zastąpień delegatów ustawionych w dostawcach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-450">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="8f6f1-451">Konfigurowanie opcji za pomocą delegata jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-451">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="8f6f1-452">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-452">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8f6f1-453">Używa delegata do konfigurowania wartości dla `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-453">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="8f6f1-454">Poniższy kod wyświetla wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-454">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8f6f1-455">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w, *appsettings.json* a następnie zastąpione przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-455">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8f6f1-456">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-456">Host versus app configuration</span></span>

<span data-ttu-id="8f6f1-457">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8f6f1-458">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8f6f1-459">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8f6f1-460">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8f6f1-461">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8f6f1-462">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="8f6f1-462">Default host configuration</span></span>

<span data-ttu-id="8f6f1-463">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8f6f1-464">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8f6f1-465">Zmienne środowiskowe poprzedzone znakiem `DOTNET_` (na przykład `DOTNET_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="8f6f1-466">Prefiks ( `DOTNET_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8f6f1-467">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8f6f1-468">Konfiguracja domyślna hosta sieci Web ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="8f6f1-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8f6f1-469">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8f6f1-470">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8f6f1-471">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` zmienna środowiskowa jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8f6f1-472">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8f6f1-473">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="8f6f1-473">Other configuration</span></span>

<span data-ttu-id="8f6f1-474">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8f6f1-475">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8f6f1-476">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8f6f1-477">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8f6f1-478">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8f6f1-479">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8f6f1-480">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-480">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="8f6f1-481">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8f6f1-482">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="8f6f1-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="8f6f1-483"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8f6f1-484">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f6f1-485">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8f6f1-485">Additional resources</span></span>

* [<span data-ttu-id="8f6f1-486">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8f6f1-487">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8f6f1-488">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8f6f1-489">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8f6f1-489">Azure Key Vault</span></span>
* <span data-ttu-id="8f6f1-490">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8f6f1-490">Azure App Configuration</span></span>
* <span data-ttu-id="8f6f1-491">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-491">Command-line arguments</span></span>
* <span data-ttu-id="8f6f1-492">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="8f6f1-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8f6f1-493">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="8f6f1-493">Directory files</span></span>
* <span data-ttu-id="8f6f1-494">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-494">Environment variables</span></span>
* <span data-ttu-id="8f6f1-495">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="8f6f1-495">In-memory .NET objects</span></span>
* <span data-ttu-id="8f6f1-496">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="8f6f1-496">Settings files</span></span>

<span data-ttu-id="8f6f1-497">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8f6f1-498">Przykłady kodu, które obserwują i w przykładowej aplikacji używają <xref:Microsoft.Extensions.Configuration> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8f6f1-499">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8f6f1-500">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8f6f1-501">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8f6f1-502">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8f6f1-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8f6f1-503">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-503">Host versus app configuration</span></span>

<span data-ttu-id="8f6f1-504">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8f6f1-505">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8f6f1-506">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8f6f1-507">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8f6f1-508">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8f6f1-509">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="8f6f1-509">Other configuration</span></span>

<span data-ttu-id="8f6f1-510">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8f6f1-511">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8f6f1-512">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8f6f1-513">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8f6f1-514">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8f6f1-515">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8f6f1-516">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8f6f1-517">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="8f6f1-517">Default configuration</span></span>

<span data-ttu-id="8f6f1-518">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych szablonów dotnet](/dotnet/core/tools/dotnet-new) są wywoływane <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8f6f1-519">`CreateDefaultBuilder` zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8f6f1-520">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8f6f1-521">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8f6f1-522">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8f6f1-523">Zmienne środowiskowe poprzedzone znakiem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8f6f1-524">Prefiks ( `ASPNETCORE_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8f6f1-525">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8f6f1-526">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="8f6f1-527">*appsettings.json* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8f6f1-528">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8f6f1-529">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8f6f1-530">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8f6f1-531">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8f6f1-532">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-532">Security</span></span>

<span data-ttu-id="8f6f1-533">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8f6f1-534">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8f6f1-535">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8f6f1-536">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8f6f1-537">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8f6f1-538"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-538"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8f6f1-539">Menedżer wpisów tajnych używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8f6f1-540">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8f6f1-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8f6f1-542">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8f6f1-543">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-543">Hierarchical configuration data</span></span>

<span data-ttu-id="8f6f1-544">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8f6f1-545">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8f6f1-546">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8f6f1-547">Sekcje i klucze są spłaszczone przy użyciu dwukropka (), `:` Aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8f6f1-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-548">section0:key0</span></span>
* <span data-ttu-id="8f6f1-549">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-549">section0:key1</span></span>
* <span data-ttu-id="8f6f1-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-550">section1:key0</span></span>
* <span data-ttu-id="8f6f1-551">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-551">section1:key1</span></span>

<span data-ttu-id="8f6f1-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8f6f1-553">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8f6f1-554">Konwencje</span><span class="sxs-lookup"><span data-stu-id="8f6f1-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8f6f1-555">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-555">Sources and providers</span></span>

<span data-ttu-id="8f6f1-556">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8f6f1-557">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8f6f1-558">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8f6f1-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8f6f1-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> można wstrzyknąć do Razor stron <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC, <xref:Microsoft.AspNetCore.Mvc.Controller> Aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8f6f1-561">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8f6f1-562">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8f6f1-563">Klucze</span><span class="sxs-lookup"><span data-stu-id="8f6f1-563">Keys</span></span>

<span data-ttu-id="8f6f1-564">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8f6f1-565">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-565">Keys are case-insensitive.</span></span> <span data-ttu-id="8f6f1-566">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8f6f1-567">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="8f6f1-568">Aby uzyskać więcej informacji na temat zduplikowanych kluczy JSON, zobacz [ten problem](https://github.com/dotnet/extensions/issues/2381)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-568">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="8f6f1-569">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="8f6f1-569">Hierarchical keys</span></span>
  * <span data-ttu-id="8f6f1-570">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-570">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8f6f1-571">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-571">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8f6f1-572">Podwójne podkreślenie ( `__` ) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-572">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8f6f1-573">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-573">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8f6f1-574">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-574">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8f6f1-575"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-575">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8f6f1-576">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-576">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8f6f1-577">Wartości</span><span class="sxs-lookup"><span data-stu-id="8f6f1-577">Values</span></span>

<span data-ttu-id="8f6f1-578">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-578">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8f6f1-579">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-579">Values are strings.</span></span>
* <span data-ttu-id="8f6f1-580">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-580">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8f6f1-581">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-581">Providers</span></span>

<span data-ttu-id="8f6f1-582">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-582">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8f6f1-583">Dostawca</span><span class="sxs-lookup"><span data-stu-id="8f6f1-583">Provider</span></span> | <span data-ttu-id="8f6f1-584">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="8f6f1-584">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8f6f1-585">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące *zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="8f6f1-585">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8f6f1-586">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8f6f1-586">Azure Key Vault</span></span> |
| <span data-ttu-id="8f6f1-587">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="8f6f1-587">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8f6f1-588">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8f6f1-588">Azure App Configuration</span></span> |
| [<span data-ttu-id="8f6f1-589">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-589">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8f6f1-590">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-590">Command-line parameters</span></span> |
| [<span data-ttu-id="8f6f1-591">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-591">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8f6f1-592">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-592">Custom source</span></span> |
| [<span data-ttu-id="8f6f1-593">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-593">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8f6f1-594">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-594">Environment variables</span></span> |
| [<span data-ttu-id="8f6f1-595">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-595">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8f6f1-596">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="8f6f1-596">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8f6f1-597">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-597">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8f6f1-598">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="8f6f1-598">Directory files</span></span> |
| [<span data-ttu-id="8f6f1-599">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="8f6f1-599">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8f6f1-600">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="8f6f1-600">In-memory collections</span></span> |
| <span data-ttu-id="8f6f1-601">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tematy dotyczące *zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="8f6f1-601">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8f6f1-602">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="8f6f1-602">File in the user profile directory</span></span> |

<span data-ttu-id="8f6f1-603">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-603">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8f6f1-604">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-604">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8f6f1-605">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-605">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8f6f1-606">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-606">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8f6f1-607">Pliki ( *appsettings.json* , *appSettings. { Environment}. JSON*, gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="8f6f1-607">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8f6f1-608">Usługa Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8f6f1-608">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8f6f1-609">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="8f6f1-609">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8f6f1-610">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="8f6f1-610">Environment variables</span></span>
1. <span data-ttu-id="8f6f1-611">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-611">Command-line arguments</span></span>

<span data-ttu-id="8f6f1-612">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-612">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8f6f1-613">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu programu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-613">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8f6f1-614">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-614">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8f6f1-615">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="8f6f1-615">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8f6f1-616">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-616">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8f6f1-617">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8f6f1-617">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8f6f1-618">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-618">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8f6f1-619">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-619">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8f6f1-620">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-620">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8f6f1-621">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="8f6f1-621">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8f6f1-622">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder` , najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-622">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8f6f1-623">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-623">Consume configuration during app startup</span></span>

<span data-ttu-id="8f6f1-624">Konfiguracja dostarczona do aplikacji w programie `ConfigureAppConfiguration` jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-624">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8f6f1-625">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-625">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8f6f1-626">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-626">Command-line Configuration Provider</span></span>

<span data-ttu-id="8f6f1-627"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-627">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8f6f1-628">Aby uaktywnić konfigurację wiersza polecenia, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-628">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8f6f1-629">`AddCommandLine` jest wywoływana automatycznie, gdy `CreateDefaultBuilder(string [])` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-629">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8f6f1-630">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-630">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8f6f1-631">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-631">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8f6f1-632">Opcjonalna konfiguracja z *appsettings.json* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-632">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8f6f1-633">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-633">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8f6f1-634">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-634">Environment variables.</span></span>

<span data-ttu-id="8f6f1-635">`CreateDefaultBuilder` dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-635">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8f6f1-636">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-636">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8f6f1-637">`CreateDefaultBuilder` działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-637">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8f6f1-638">W związku z tym konfiguracja wiersza polecenia aktywowana przez program `CreateDefaultBuilder` może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-638">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8f6f1-639">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-639">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8f6f1-640">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj jako `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-640">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8f6f1-641">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-641">**Example**</span></span>

<span data-ttu-id="8f6f1-642">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-642">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8f6f1-643">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-643">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8f6f1-644">Podaj do polecenia argument wiersza polecenia `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-644">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8f6f1-645">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-645">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8f6f1-646">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-646">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8f6f1-647">Argumenty</span><span class="sxs-lookup"><span data-stu-id="8f6f1-647">Arguments</span></span>

<span data-ttu-id="8f6f1-648">Wartość musi następować po znaku równości ( `=` ) lub klucz musi mieć prefiks ( `--` lub `/` ), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-648">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8f6f1-649">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-649">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8f6f1-650">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="8f6f1-650">Key prefix</span></span>               | <span data-ttu-id="8f6f1-651">Przykład</span><span class="sxs-lookup"><span data-stu-id="8f6f1-651">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8f6f1-652">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="8f6f1-652">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8f6f1-653">Dwie kreski ( `--` )</span><span class="sxs-lookup"><span data-stu-id="8f6f1-653">Two dashes (`--`)</span></span>        | <span data-ttu-id="8f6f1-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8f6f1-655">Ukośnik ( `/` )</span><span class="sxs-lookup"><span data-stu-id="8f6f1-655">Forward slash (`/`)</span></span>      | <span data-ttu-id="8f6f1-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8f6f1-657">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-657">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8f6f1-658">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-658">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8f6f1-659">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="8f6f1-659">Switch mappings</span></span>

<span data-ttu-id="8f6f1-660">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-660">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8f6f1-661">Podczas ręcznego kompilowania konfiguracji za pomocą programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-661">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8f6f1-662">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-662">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8f6f1-663">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-663">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8f6f1-664">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-664">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8f6f1-665">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-665">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8f6f1-666">Przełączniki muszą zaczynać się kreską ( `-` ) lub podwójną kreską ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-666">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8f6f1-667">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-667">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8f6f1-668">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-668">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8f6f1-669">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-669">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8f6f1-670">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-670">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8f6f1-671">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-671">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8f6f1-672">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-672">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8f6f1-673">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-673">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8f6f1-674">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-674">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8f6f1-675">Klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-675">Key</span></span>       | <span data-ttu-id="8f6f1-676">Wartość</span><span class="sxs-lookup"><span data-stu-id="8f6f1-676">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8f6f1-677">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-677">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8f6f1-678">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-678">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8f6f1-679">Klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-679">Key</span></span>               | <span data-ttu-id="8f6f1-680">Wartość</span><span class="sxs-lookup"><span data-stu-id="8f6f1-680">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8f6f1-681">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="8f6f1-681">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8f6f1-682"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-682">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8f6f1-683">Aby uaktywnić konfigurację zmiennych środowiskowych, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-683">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8f6f1-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8f6f1-685">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-685">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8f6f1-686">`AddEnvironmentVariables` służy do ładowania zmiennych środowiskowych, które są poprzedzone `ASPNETCORE_` [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-686">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8f6f1-687">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-687">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8f6f1-688">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-688">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8f6f1-689">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez wywołanie `AddEnvironmentVariables` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-689">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8f6f1-690">Opcjonalna konfiguracja z *appsettings.json* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-690">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8f6f1-691">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-691">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8f6f1-692">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-692">Command-line arguments.</span></span>

<span data-ttu-id="8f6f1-693">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-693">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8f6f1-694">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-694">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8f6f1-695">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddEnvironmentVariables` z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-695">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8f6f1-696">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-696">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8f6f1-697">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-697">**Example**</span></span>

<span data-ttu-id="8f6f1-698">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-698">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8f6f1-699">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-699">Run the sample app.</span></span> <span data-ttu-id="8f6f1-700">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-700">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8f6f1-701">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej środowiskowej `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-701">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8f6f1-702">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj w `Development` przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-702">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8f6f1-703">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-703">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8f6f1-704">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-704">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8f6f1-705">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić `FilteredConfiguration` stronę na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-705">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8f6f1-706">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-706">Prefixes</span></span>

<span data-ttu-id="8f6f1-707">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-707">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8f6f1-708">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_` , podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-708">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8f6f1-709">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-709">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8f6f1-710">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-710">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8f6f1-711">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-711">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8f6f1-712">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-712">**Connection string prefixes**</span></span>

<span data-ttu-id="8f6f1-713">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-713">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8f6f1-714">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-714">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8f6f1-715">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="8f6f1-715">Connection string prefix</span></span> | <span data-ttu-id="8f6f1-716">Dostawca</span><span class="sxs-lookup"><span data-stu-id="8f6f1-716">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8f6f1-717">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-717">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8f6f1-718">MySQL</span><span class="sxs-lookup"><span data-stu-id="8f6f1-718">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8f6f1-719">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8f6f1-719">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8f6f1-720">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8f6f1-720">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8f6f1-721">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-721">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8f6f1-722">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-722">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8f6f1-723">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-723">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8f6f1-724">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="8f6f1-724">Environment variable key</span></span> | <span data-ttu-id="8f6f1-725">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-725">Converted configuration key</span></span> | <span data-ttu-id="8f6f1-726">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="8f6f1-726">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-727">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-727">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-728">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8f6f1-729">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-729">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-730">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8f6f1-731">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-731">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8f6f1-732">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-732">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8f6f1-733">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-733">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="8f6f1-734">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-734">**Example**</span></span>

<span data-ttu-id="8f6f1-735">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-735">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8f6f1-736">Nazwa: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-736">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8f6f1-737">Wartość: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8f6f1-737">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8f6f1-738">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config` , odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-738">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8f6f1-739">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-739">File Configuration Provider</span></span>

<span data-ttu-id="8f6f1-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8f6f1-741">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-741">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8f6f1-742">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="8f6f1-742">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8f6f1-743">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="8f6f1-743">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8f6f1-744">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="8f6f1-744">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8f6f1-745">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="8f6f1-745">INI Configuration Provider</span></span>

<span data-ttu-id="8f6f1-746"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-746">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8f6f1-747">Aby uaktywnić konfigurację pliku INI, wywołaj <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-747">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8f6f1-748">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-748">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8f6f1-749">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-749">Overloads permit specifying:</span></span>

* <span data-ttu-id="8f6f1-750">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-750">Whether the file is optional.</span></span>
* <span data-ttu-id="8f6f1-751">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-751">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8f6f1-752"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-752">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8f6f1-753">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-753">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8f6f1-754">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-754">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8f6f1-755">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-755">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8f6f1-756">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-756">section0:key0</span></span>
* <span data-ttu-id="8f6f1-757">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-757">section0:key1</span></span>
* <span data-ttu-id="8f6f1-758">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="8f6f1-758">section1:subsection:key</span></span>
* <span data-ttu-id="8f6f1-759">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-759">section2:subsection0:key</span></span>
* <span data-ttu-id="8f6f1-760">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-760">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8f6f1-761">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="8f6f1-761">JSON Configuration Provider</span></span>

<span data-ttu-id="8f6f1-762"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-762">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8f6f1-763">Aby uaktywnić konfigurację pliku JSON, wywołaj <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-763">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8f6f1-764">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-764">Overloads permit specifying:</span></span>

* <span data-ttu-id="8f6f1-765">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-765">Whether the file is optional.</span></span>
* <span data-ttu-id="8f6f1-766">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-766">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8f6f1-767"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-767">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8f6f1-768">`AddJsonFile` jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-768">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8f6f1-769">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-769">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8f6f1-770">*appsettings.json*: Ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-770">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="8f6f1-771">Wersja środowiska pliku może przesłonić wartości dostarczone przez *appsettings.json* plik.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-771">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8f6f1-772">*appSettings. {Environment}. JSON*: wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-772">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8f6f1-773">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-773">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8f6f1-774">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-774">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8f6f1-775">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-775">Environment variables.</span></span>
* <span data-ttu-id="8f6f1-776">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-776">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8f6f1-777">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-777">Command-line arguments.</span></span>

<span data-ttu-id="8f6f1-778">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-778">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8f6f1-779">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-779">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8f6f1-780">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *appsettings.json* i *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-780">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8f6f1-781">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-781">**Example**</span></span>

<span data-ttu-id="8f6f1-782">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-782">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8f6f1-783">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-783">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8f6f1-784">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-784">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8f6f1-785">W przypadku *appsettings.Development.js* w aplikacji przykładowej załadowano następujący plik:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-785">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8f6f1-786">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-786">Run the sample app.</span></span> <span data-ttu-id="8f6f1-787">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-787">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8f6f1-788">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-788">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8f6f1-789">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-789">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8f6f1-790">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-790">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8f6f1-791">Otwórz *Właściwości/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-791">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8f6f1-792">W `ConfigurationSample` profilu Zmień wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej na `Production` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-792">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8f6f1-793">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-793">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8f6f1-794">Ustawienia w *appsettings.Development.js* nie przesłaniają już ustawień w programie *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-794">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8f6f1-795">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-795">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8f6f1-796">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="8f6f1-796">XML Configuration Provider</span></span>

<span data-ttu-id="8f6f1-797"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-797">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8f6f1-798">Aby uaktywnić konfigurację pliku XML, wywołaj <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-798">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8f6f1-799">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-799">Overloads permit specifying:</span></span>

* <span data-ttu-id="8f6f1-800">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-800">Whether the file is optional.</span></span>
* <span data-ttu-id="8f6f1-801">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-801">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8f6f1-802"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-802">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8f6f1-803">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-803">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8f6f1-804">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-804">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8f6f1-805">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-805">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8f6f1-806">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-806">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8f6f1-807">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8f6f1-808">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-808">section0:key0</span></span>
* <span data-ttu-id="8f6f1-809">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-809">section0:key1</span></span>
* <span data-ttu-id="8f6f1-810">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-810">section1:key0</span></span>
* <span data-ttu-id="8f6f1-811">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-811">section1:key1</span></span>

<span data-ttu-id="8f6f1-812">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-812">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8f6f1-813">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8f6f1-814">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-814">section:section0:key:key0</span></span>
* <span data-ttu-id="8f6f1-815">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-815">section:section0:key:key1</span></span>
* <span data-ttu-id="8f6f1-816">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-816">section:section1:key:key0</span></span>
* <span data-ttu-id="8f6f1-817">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-817">section:section1:key:key1</span></span>

<span data-ttu-id="8f6f1-818">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-818">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8f6f1-819">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="8f6f1-819">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8f6f1-820">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="8f6f1-820">key:attribute</span></span>
* <span data-ttu-id="8f6f1-821">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="8f6f1-821">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8f6f1-822">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="8f6f1-822">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8f6f1-823"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-823">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8f6f1-824">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-824">The key is the file name.</span></span> <span data-ttu-id="8f6f1-825">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-825">The value contains the file's contents.</span></span> <span data-ttu-id="8f6f1-826">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-826">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8f6f1-827">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-827">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8f6f1-828">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-828">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8f6f1-829">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-829">Overloads permit specifying:</span></span>

* <span data-ttu-id="8f6f1-830">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-830">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8f6f1-831">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-831">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8f6f1-832">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-832">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8f6f1-833">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-833">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8f6f1-834">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8f6f1-835">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="8f6f1-835">Memory Configuration Provider</span></span>

<span data-ttu-id="8f6f1-836"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-836">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8f6f1-837">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-837">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8f6f1-838">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-838">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8f6f1-839">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-839">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8f6f1-840">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-840">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8f6f1-841">Słownik jest używany z wywołaniem do `AddInMemoryCollection` zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-841">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8f6f1-842">GetValue</span><span class="sxs-lookup"><span data-stu-id="8f6f1-842">GetValue</span></span>

<span data-ttu-id="8f6f1-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8f6f1-844">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-844">An overload accepts a default value.</span></span>

<span data-ttu-id="8f6f1-845">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-845">The following example:</span></span>

* <span data-ttu-id="8f6f1-846">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-846">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8f6f1-847">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-847">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8f6f1-848">Typ wartości `int` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-848">Types the value as an `int`.</span></span>
* <span data-ttu-id="8f6f1-849">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-849">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8f6f1-850">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="8f6f1-850">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8f6f1-851">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-851">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8f6f1-852">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-852">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8f6f1-853">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-853">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8f6f1-854">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-854">section0:key0</span></span>
* <span data-ttu-id="8f6f1-855">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-855">section0:key1</span></span>
* <span data-ttu-id="8f6f1-856">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-856">section1:key0</span></span>
* <span data-ttu-id="8f6f1-857">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-857">section1:key1</span></span>
* <span data-ttu-id="8f6f1-858">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-858">section2:subsection0:key0</span></span>
* <span data-ttu-id="8f6f1-859">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-859">section2:subsection0:key1</span></span>
* <span data-ttu-id="8f6f1-860">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-860">section2:subsection1:key0</span></span>
* <span data-ttu-id="8f6f1-861">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-861">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8f6f1-862">GetSection</span><span class="sxs-lookup"><span data-stu-id="8f6f1-862">GetSection</span></span>

<span data-ttu-id="8f6f1-863">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8f6f1-864">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1` , wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-864">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8f6f1-865">`configSection`Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-865">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8f6f1-866">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0` , wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-866">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8f6f1-867">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-867">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8f6f1-868">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-868">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8f6f1-869">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-869">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8f6f1-870">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-870">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8f6f1-871">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="8f6f1-871">GetChildren</span></span>

<span data-ttu-id="8f6f1-872">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w programie `section2` uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-872">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8f6f1-873">Exists</span><span class="sxs-lookup"><span data-stu-id="8f6f1-873">Exists</span></span>

<span data-ttu-id="8f6f1-874">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-874">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8f6f1-875">Dane przykładowe są spowodowane tym, że `sectionExists` `false` `section2:subsection2` w danych konfiguracji nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-875">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8f6f1-876">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="8f6f1-876">Bind to an object graph</span></span>

<span data-ttu-id="8f6f1-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8f6f1-878">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-878">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8f6f1-879">Przykład zawiera `TvShow` model, którego obiekt zawiera obiekty `Metadata` i `Actors` klasy (*modele/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="8f6f1-879">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8f6f1-880">Przykładowa aplikacja zawiera plik *tvshow.xml* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-880">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8f6f1-881">Konfiguracja jest powiązana z `TvShow` wykresem całego obiektu za pomocą `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-881">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8f6f1-882">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-882">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8f6f1-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8f6f1-884">`Get<T>` jest wygodniejszy niż używanie `Bind` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-884">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8f6f1-885">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-885">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8f6f1-886">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="8f6f1-886">Bind an array to a class</span></span>

<span data-ttu-id="8f6f1-887">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="8f6f1-887">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8f6f1-888"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-888">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8f6f1-889">Każdy format tablicy, który ujawnia segment klucza numerycznego ( `:0:` , `:1:` , &hellip; `:{n}:` ) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-889">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8f6f1-890">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-890">Binding is provided by convention.</span></span> <span data-ttu-id="8f6f1-891">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-891">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8f6f1-892">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-892">**In-memory array processing**</span></span>

<span data-ttu-id="8f6f1-893">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-893">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8f6f1-894">Klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-894">Key</span></span>             | <span data-ttu-id="8f6f1-895">Wartość</span><span class="sxs-lookup"><span data-stu-id="8f6f1-895">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8f6f1-896">Tablica: wpisy: 0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-896">array:entries:0</span></span> | <span data-ttu-id="8f6f1-897">value0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-897">value0</span></span> |
| <span data-ttu-id="8f6f1-898">Tablica: wpisy: 1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-898">array:entries:1</span></span> | <span data-ttu-id="8f6f1-899">sekwencj</span><span class="sxs-lookup"><span data-stu-id="8f6f1-899">value1</span></span> |
| <span data-ttu-id="8f6f1-900">Tablica: wpisy: 2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-900">array:entries:2</span></span> | <span data-ttu-id="8f6f1-901">wartość2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-901">value2</span></span> |
| <span data-ttu-id="8f6f1-902">Tablica: wpisy: 4</span><span class="sxs-lookup"><span data-stu-id="8f6f1-902">array:entries:4</span></span> | <span data-ttu-id="8f6f1-903">value4</span><span class="sxs-lookup"><span data-stu-id="8f6f1-903">value4</span></span> |
| <span data-ttu-id="8f6f1-904">Tablica: wpisy: 5</span><span class="sxs-lookup"><span data-stu-id="8f6f1-904">array:entries:5</span></span> | <span data-ttu-id="8f6f1-905">value5</span><span class="sxs-lookup"><span data-stu-id="8f6f1-905">value5</span></span> |

<span data-ttu-id="8f6f1-906">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-906">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8f6f1-907">Tablica pomija wartość dla indeksu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-907">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8f6f1-908">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-908">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8f6f1-909">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-909">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8f6f1-910">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-910">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8f6f1-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8f6f1-912">Obiekt powiązany, wystąpienie elementu `ArrayExample` , otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-912">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8f6f1-913">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="8f6f1-913">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8f6f1-914">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="8f6f1-914">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8f6f1-915">0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-915">0</span></span>                            | <span data-ttu-id="8f6f1-916">value0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-916">value0</span></span>                       |
| <span data-ttu-id="8f6f1-917">1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-917">1</span></span>                            | <span data-ttu-id="8f6f1-918">sekwencj</span><span class="sxs-lookup"><span data-stu-id="8f6f1-918">value1</span></span>                       |
| <span data-ttu-id="8f6f1-919">2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-919">2</span></span>                            | <span data-ttu-id="8f6f1-920">wartość2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-920">value2</span></span>                       |
| <span data-ttu-id="8f6f1-921">3</span><span class="sxs-lookup"><span data-stu-id="8f6f1-921">3</span></span>                            | <span data-ttu-id="8f6f1-922">value4</span><span class="sxs-lookup"><span data-stu-id="8f6f1-922">value4</span></span>                       |
| <span data-ttu-id="8f6f1-923">4</span><span class="sxs-lookup"><span data-stu-id="8f6f1-923">4</span></span>                            | <span data-ttu-id="8f6f1-924">value5</span><span class="sxs-lookup"><span data-stu-id="8f6f1-924">value5</span></span>                       |

<span data-ttu-id="8f6f1-925">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-925">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8f6f1-926">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-926">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8f6f1-927">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-927">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8f6f1-928">Brakujący element konfiguracji dla indeksu &num; 3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-928">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8f6f1-929">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-929">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8f6f1-930">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-930">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8f6f1-931">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-931">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8f6f1-932">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-932">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8f6f1-933">Klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-933">Key</span></span>             | <span data-ttu-id="8f6f1-934">Wartość</span><span class="sxs-lookup"><span data-stu-id="8f6f1-934">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8f6f1-935">Tablica: wpisy: 3</span><span class="sxs-lookup"><span data-stu-id="8f6f1-935">array:entries:3</span></span> | <span data-ttu-id="8f6f1-936">Wartość3</span><span class="sxs-lookup"><span data-stu-id="8f6f1-936">value3</span></span> |

<span data-ttu-id="8f6f1-937">Jeśli `ArrayExample` wystąpienie klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num; 3, `ArrayExample.Entries` Tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-937">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8f6f1-938">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="8f6f1-938">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8f6f1-939">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="8f6f1-939">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8f6f1-940">0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-940">0</span></span>                            | <span data-ttu-id="8f6f1-941">value0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-941">value0</span></span>                       |
| <span data-ttu-id="8f6f1-942">1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-942">1</span></span>                            | <span data-ttu-id="8f6f1-943">sekwencj</span><span class="sxs-lookup"><span data-stu-id="8f6f1-943">value1</span></span>                       |
| <span data-ttu-id="8f6f1-944">2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-944">2</span></span>                            | <span data-ttu-id="8f6f1-945">wartość2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-945">value2</span></span>                       |
| <span data-ttu-id="8f6f1-946">3</span><span class="sxs-lookup"><span data-stu-id="8f6f1-946">3</span></span>                            | <span data-ttu-id="8f6f1-947">Wartość3</span><span class="sxs-lookup"><span data-stu-id="8f6f1-947">value3</span></span>                       |
| <span data-ttu-id="8f6f1-948">4</span><span class="sxs-lookup"><span data-stu-id="8f6f1-948">4</span></span>                            | <span data-ttu-id="8f6f1-949">value4</span><span class="sxs-lookup"><span data-stu-id="8f6f1-949">value4</span></span>                       |
| <span data-ttu-id="8f6f1-950">5</span><span class="sxs-lookup"><span data-stu-id="8f6f1-950">5</span></span>                            | <span data-ttu-id="8f6f1-951">value5</span><span class="sxs-lookup"><span data-stu-id="8f6f1-951">value5</span></span>                       |

<span data-ttu-id="8f6f1-952">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="8f6f1-952">**JSON array processing**</span></span>

<span data-ttu-id="8f6f1-953">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-953">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8f6f1-954">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-954">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8f6f1-955">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-955">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8f6f1-956">Klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-956">Key</span></span>                     | <span data-ttu-id="8f6f1-957">Wartość</span><span class="sxs-lookup"><span data-stu-id="8f6f1-957">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8f6f1-958">json_array: klucz</span><span class="sxs-lookup"><span data-stu-id="8f6f1-958">json_array:key</span></span>          | <span data-ttu-id="8f6f1-959">wartośća</span><span class="sxs-lookup"><span data-stu-id="8f6f1-959">valueA</span></span> |
| <span data-ttu-id="8f6f1-960">json_array: podsekcja: 0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-960">json_array:subsection:0</span></span> | <span data-ttu-id="8f6f1-961">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="8f6f1-961">valueB</span></span> |
| <span data-ttu-id="8f6f1-962">json_array: podsekcja: 1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-962">json_array:subsection:1</span></span> | <span data-ttu-id="8f6f1-963">valueC</span><span class="sxs-lookup"><span data-stu-id="8f6f1-963">valueC</span></span> |
| <span data-ttu-id="8f6f1-964">json_array: podsekcja: 2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-964">json_array:subsection:2</span></span> | <span data-ttu-id="8f6f1-965">Znajdując</span><span class="sxs-lookup"><span data-stu-id="8f6f1-965">valueD</span></span> |

<span data-ttu-id="8f6f1-966">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-966">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8f6f1-967">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-967">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8f6f1-968">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-968">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8f6f1-969">`JsonArrayExample.Subsection` Indeks</span><span class="sxs-lookup"><span data-stu-id="8f6f1-969">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8f6f1-970">`JsonArrayExample.Subsection` Wartościami</span><span class="sxs-lookup"><span data-stu-id="8f6f1-970">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8f6f1-971">0</span><span class="sxs-lookup"><span data-stu-id="8f6f1-971">0</span></span>                                   | <span data-ttu-id="8f6f1-972">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="8f6f1-972">valueB</span></span>                              |
| <span data-ttu-id="8f6f1-973">1</span><span class="sxs-lookup"><span data-stu-id="8f6f1-973">1</span></span>                                   | <span data-ttu-id="8f6f1-974">valueC</span><span class="sxs-lookup"><span data-stu-id="8f6f1-974">valueC</span></span>                              |
| <span data-ttu-id="8f6f1-975">2</span><span class="sxs-lookup"><span data-stu-id="8f6f1-975">2</span></span>                                   | <span data-ttu-id="8f6f1-976">Znajdując</span><span class="sxs-lookup"><span data-stu-id="8f6f1-976">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8f6f1-977">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8f6f1-977">Custom configuration provider</span></span>

<span data-ttu-id="8f6f1-978">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-978">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8f6f1-979">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-979">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8f6f1-980">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-980">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8f6f1-981">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-981">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8f6f1-982">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-982">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8f6f1-983">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-983">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8f6f1-984">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-984">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8f6f1-985">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-985">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8f6f1-986">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-986">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8f6f1-987">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-987">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8f6f1-988">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8f6f1-989">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-989">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8f6f1-990">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8f6f1-991">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-991">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8f6f1-992">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-992">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8f6f1-993">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8f6f1-994">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-994">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8f6f1-995">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-995">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8f6f1-996">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-996">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8f6f1-997">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="8f6f1-997">Access configuration during startup</span></span>

<span data-ttu-id="8f6f1-998">Wsuń `IConfiguration` do `Startup` konstruktora, aby uzyskać dostęp do wartości konfiguracyjnych w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8f6f1-998">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8f6f1-999">Aby uzyskać dostęp do konfiguracji w programie `Startup.Configure` , należy wstrzyknąć `IConfiguration` bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-999">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8f6f1-1000">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1000">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="8f6f1-1001">Konfiguracja dostępu na Razor stronie stron lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1001">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8f6f1-1002">Aby uzyskać dostęp do ustawień konfiguracji na Razor stronie stron lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [ przestrzeni nazwMicrosoft.Extensions.Configwersja](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1002">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8f6f1-1003">Na Razor stronie stron:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1003">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8f6f1-1004">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1004">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8f6f1-1005">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1005">Add configuration from an external assembly</span></span>

<span data-ttu-id="8f6f1-1006"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1006">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8f6f1-1007">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1007">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f6f1-1008">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8f6f1-1008">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
