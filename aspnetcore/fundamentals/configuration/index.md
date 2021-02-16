---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core przy użyciu interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
ms.openlocfilehash: 0f069b049889f7caade493e238ac7a23db5e79af
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536327"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="91dd1-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91dd1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="91dd1-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="91dd1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91dd1-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="91dd1-106">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="91dd1-107">Pliki ustawień, takie jak *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="91dd1-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="91dd1-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-108">Environment variables</span></span>
* <span data-ttu-id="91dd1-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91dd1-109">Azure Key Vault</span></span>
* <span data-ttu-id="91dd1-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="91dd1-110">Azure App Configuration</span></span>
* <span data-ttu-id="91dd1-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-111">Command-line arguments</span></span>
* <span data-ttu-id="91dd1-112">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="91dd1-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="91dd1-113">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="91dd1-113">Directory files</span></span>
* <span data-ttu-id="91dd1-114">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="91dd1-114">In-memory .NET objects</span></span>

<span data-ttu-id="91dd1-115">Ten temat zawiera informacje dotyczące konfiguracji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91dd1-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="91dd1-116">Aby uzyskać informacje na temat korzystania z konfiguracji w aplikacjach konsolowych, zobacz [Konfiguracja platformy .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="91dd1-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="91dd1-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91dd1-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="91dd1-118">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="91dd1-118">Default configuration</span></span>

<span data-ttu-id="91dd1-119">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="91dd1-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="91dd1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="91dd1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="91dd1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący element `IConfiguration` jako źródło.</span><span class="sxs-lookup"><span data-stu-id="91dd1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="91dd1-122">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="91dd1-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="91dd1-123">[appsettings.json](#appsettingsjson) Korzystanie z [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="91dd1-124">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="91dd1-125">Na przykład *AppSettings*. ***Produkcja \* \* _._json* i *AppSettings*. \* \* \* programowanie** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="91dd1-126">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="91dd1-127">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="91dd1-128">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="91dd1-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="91dd1-129">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="91dd1-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="91dd1-130">Na przykład jeśli `MyKey` jest ustawiona w obu *appsettings.json* i środowisku, wartość środowiska jest używana.</span><span class="sxs-lookup"><span data-stu-id="91dd1-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="91dd1-131">Przy użyciu domyślnych dostawców konfiguracji  [dostawca konfiguracji wiersza polecenia](#clcp) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="91dd1-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="91dd1-132">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="91dd1-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="91dd1-133">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="91dd1-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="91dd1-134">Weź pod uwagę następujący *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="91dd1-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="91dd1-135">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-136">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="91dd1-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="91dd1-137">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings*. ***Produkcja \* \* _._json* i *AppSettings*. \* \* \* pliki deweloperskie** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="91dd1-138">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="91dd1-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="91dd1-139">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="91dd1-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="91dd1-140">*AppSettings*. `Environment` . wartości *JSON* przesłaniają klucze w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="91dd1-141">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-141">For example, by default:</span></span>

* <span data-ttu-id="91dd1-142">W programowaniu, *AppSettings*. \***Development** _._json \* konfiguracja zastępuje wartości Znalezione w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="91dd1-143">W środowisku produkcyjnym *AppSettings*. \***Production** _._json \* konfiguracja zastępuje wartości Znalezione w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="91dd1-144">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="91dd1-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="91dd1-145">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="91dd1-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="91dd1-146">Przy użyciu konfiguracji [domyślnej](#default) , *appsettings.json* a *appSettings.* `Environment` pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="91dd1-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="91dd1-147">Zmiany wprowadzone w *appsettings.json* i *appSettings.* `Environment` plik *. JSON* ***po*** uruchomieniu aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="91dd1-148">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="91dd1-149">Łączenie kolekcji usług</span><span class="sxs-lookup"><span data-stu-id="91dd1-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="91dd1-150">Zabezpieczenia i wpisy tajne użytkownika</span><span class="sxs-lookup"><span data-stu-id="91dd1-150">Security and user secrets</span></span>

<span data-ttu-id="91dd1-151">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="91dd1-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="91dd1-152">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="91dd1-153">Za pomocą narzędzia do [zarządzania kluczami tajnymi](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="91dd1-153">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="91dd1-154">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="91dd1-155">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="91dd1-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="91dd1-156">[Domyślnie](#default)Źródło konfiguracji kluczy tajnych użytkownika jest rejestrowane po źródłach konfiguracji JSON.</span><span class="sxs-lookup"><span data-stu-id="91dd1-156">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="91dd1-157">W związku z tym klucze tajne użytkownika mają pierwszeństwo przed kluczami w *appsettings.json* i *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-157">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="91dd1-158">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="91dd1-158">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="91dd1-159"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-159"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="91dd1-160">Narzędzie Secret Manager używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="91dd1-160">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="91dd1-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-161">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="91dd1-162">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91dd1-162">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="91dd1-163">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-163">Environment variables</span></span>

<span data-ttu-id="91dd1-164">Przy użyciu konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> ładowana konfiguracja ze zmiennej środowiskowej par klucz-wartość po odczytu *appsettings.json* , *appSettings.* `Environment` *. JSON* i wpisy [tajne użytkownika](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="91dd1-164">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="91dd1-165">W związku z tym kluczowe wartości są odczytywane z wartości zastąpienia środowiska odczytywane z *appsettings.json* , *appSettings.* `Environment` *. JSON* i wpisy tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="91dd1-165">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="91dd1-166">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="91dd1-166">The following `set` commands:</span></span>

* <span data-ttu-id="91dd1-167">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="91dd1-167">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="91dd1-168">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="91dd1-168">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="91dd1-169">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-169">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="91dd1-170">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="91dd1-170">The preceding environment settings:</span></span>

* <span data-ttu-id="91dd1-171">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="91dd1-171">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="91dd1-172">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="91dd1-172">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="91dd1-173">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="91dd1-173">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="91dd1-174">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="91dd1-174">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="91dd1-175">`/M` ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="91dd1-175">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="91dd1-176">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="91dd1-176">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="91dd1-177">Aby sprawdzić, czy poprzednie polecenia zastępują *appsettings.json* i *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-177">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="91dd1-178">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="91dd1-178">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="91dd1-179">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i wprowadź `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-179">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="91dd1-180">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="91dd1-180">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="91dd1-181">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-181">In the preceding code:</span></span>

* <span data-ttu-id="91dd1-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="91dd1-182">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="91dd1-183">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-183">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="91dd1-184">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="91dd1-184">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="91dd1-185">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-185">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="91dd1-186">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="91dd1-186">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="91dd1-187">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="91dd1-187">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="91dd1-188">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-188">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="91dd1-189">`DOTNET_` `ASPNETCORE_` Prefiksy i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="91dd1-189">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="91dd1-190">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="91dd1-190">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="91dd1-191">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="91dd1-191">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="91dd1-192">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="91dd1-192">Azure App Service application settings are:</span></span>

* <span data-ttu-id="91dd1-193">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="91dd1-193">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="91dd1-194">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-194">Exposed as environment variables.</span></span>

<span data-ttu-id="91dd1-195">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="91dd1-195">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="91dd1-196">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-196">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="91dd1-197">Nazewnictwo zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="91dd1-197">Naming of environment variables</span></span>

<span data-ttu-id="91dd1-198">Nazwy zmiennych środowiskowych odzwierciedlają strukturę *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-198">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="91dd1-199">Każdy element w hierarchii jest oddzielony znakiem podwójnego podkreślenia (preferowany) lub dwukropek.</span><span class="sxs-lookup"><span data-stu-id="91dd1-199">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="91dd1-200">Gdy struktura elementu zawiera tablicę, indeks tablicy powinien być traktowany jako dodatkowa nazwa elementu w tej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="91dd1-200">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="91dd1-201">Rozważmy następujący *appsettings.json* plik i jego równoważne wartości reprezentowane jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-201">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

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

<span data-ttu-id="91dd1-202">**zmienne środowiskowe**</span><span class="sxs-lookup"><span data-stu-id="91dd1-202">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="91dd1-203">Zmienne środowiskowe ustawione w wygenerowanym launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="91dd1-203">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="91dd1-204">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="91dd1-204">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="91dd1-205">Na przykład szablony sieci Web ASP.NET Core generują *launchSettings.jsw* pliku, który ustawia konfigurację punktu końcowego na:</span><span class="sxs-lookup"><span data-stu-id="91dd1-205">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="91dd1-206">Konfigurowanie `applicationUrl` `ASPNETCORE_URLS` ustawień zmienna środowiskowa i zastąpień ustawionych w środowisku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-206">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="91dd1-207">Zmienne środowiskowe ucieczki w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="91dd1-207">Escape environment variables on Linux</span></span>

<span data-ttu-id="91dd1-208">W systemie Linux wartość zmiennych środowiskowych adresów URL musi być Ucieczka, aby `systemd` można było ją przeanalizować.</span><span class="sxs-lookup"><span data-stu-id="91dd1-208">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="91dd1-209">Korzystanie z narzędzia systemu Linux, `systemd-escape` które daje `http:--localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="91dd1-209">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="91dd1-210">Wyświetl zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-210">Display environment variables</span></span>

<span data-ttu-id="91dd1-211">Poniższy kod wyświetla zmienne środowiskowe i wartości podczas uruchamiania aplikacji, które mogą być przydatne podczas debugowania ustawień środowiska:</span><span class="sxs-lookup"><span data-stu-id="91dd1-211">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="91dd1-212">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-212">Command-line</span></span>

<span data-ttu-id="91dd1-213">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-213">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="91dd1-214">*appsettings.json* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-214">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="91dd1-215">Wpisy [tajne aplikacji](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="91dd1-215">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="91dd1-216">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-216">Environment variables.</span></span>

<span data-ttu-id="91dd1-217">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-217">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="91dd1-218">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-218">Command-line arguments</span></span>

<span data-ttu-id="91dd1-219">Następujące polecenie ustawia klucze i wartości przy użyciu `=` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-219">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="91dd1-220">Następujące polecenie ustawia klucze i wartości przy użyciu `/` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-220">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="91dd1-221">Następujące polecenie ustawia klucze i wartości przy użyciu `--` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-221">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="91dd1-222">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="91dd1-222">The key value:</span></span>

* <span data-ttu-id="91dd1-223">Musi `=` być zgodna lub musi mieć prefiks lub, gdy wartość znajduje się w `--` `/` miejscu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-223">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="91dd1-224">Nie jest wymagane, jeśli `=` jest używany.</span><span class="sxs-lookup"><span data-stu-id="91dd1-224">Isn't required if `=` is used.</span></span> <span data-ttu-id="91dd1-225">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="91dd1-225">For example, `MySetting=`.</span></span>

<span data-ttu-id="91dd1-226">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które są używane `=` z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-226">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="91dd1-227">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="91dd1-227">Switch mappings</span></span>

<span data-ttu-id="91dd1-228">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="91dd1-228">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="91dd1-229">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-229">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="91dd1-230">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-230">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="91dd1-231">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-231">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="91dd1-232">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="91dd1-232">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="91dd1-233">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="91dd1-233">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="91dd1-234">Przełączniki muszą zaczynać się od `-` lub `--` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-234">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="91dd1-235">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-235">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="91dd1-236">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-236">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="91dd1-237">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="91dd1-237">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-238">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="91dd1-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="91dd1-239">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="91dd1-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="91dd1-240">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91dd1-241">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="91dd1-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="91dd1-242">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-242">Hierarchical configuration data</span></span>

<span data-ttu-id="91dd1-243">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="91dd1-244">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="91dd1-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="91dd1-245">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-246">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="91dd1-247">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="91dd1-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="91dd1-249">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="91dd1-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="91dd1-250">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-250">Configuration keys and values</span></span>

<span data-ttu-id="91dd1-251">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-251">Configuration keys:</span></span>

* <span data-ttu-id="91dd1-252">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="91dd1-252">Are case-insensitive.</span></span> <span data-ttu-id="91dd1-253">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="91dd1-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="91dd1-254">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="91dd1-255">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="91dd1-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="91dd1-256">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="91dd1-256">Hierarchical keys</span></span>
  * <span data-ttu-id="91dd1-257">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="91dd1-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="91dd1-258">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="91dd1-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="91dd1-259">Podwójne podkreślenie, `__` ,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="91dd1-260">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="91dd1-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="91dd1-261">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="91dd1-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="91dd1-263">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="91dd1-264">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-264">Configuration values:</span></span>

* <span data-ttu-id="91dd1-265">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="91dd1-265">Are strings.</span></span>
* <span data-ttu-id="91dd1-266">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="91dd1-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="91dd1-267">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-267">Configuration providers</span></span>

<span data-ttu-id="91dd1-268">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="91dd1-269">Dostawca</span><span class="sxs-lookup"><span data-stu-id="91dd1-269">Provider</span></span> | <span data-ttu-id="91dd1-270">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="91dd1-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="91dd1-271">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91dd1-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="91dd1-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91dd1-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="91dd1-273">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="91dd1-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="91dd1-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="91dd1-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="91dd1-275">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="91dd1-276">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-276">Command-line parameters</span></span> |
| [<span data-ttu-id="91dd1-277">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="91dd1-278">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-278">Custom source</span></span> |
| [<span data-ttu-id="91dd1-279">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="91dd1-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="91dd1-280">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-280">Environment variables</span></span> |
| [<span data-ttu-id="91dd1-281">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="91dd1-282">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="91dd1-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="91dd1-283">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="91dd1-284">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="91dd1-284">Directory files</span></span> |
| [<span data-ttu-id="91dd1-285">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="91dd1-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="91dd1-286">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="91dd1-286">In-memory collections</span></span> |
| [<span data-ttu-id="91dd1-287">Wpisy tajne użytkownika</span><span class="sxs-lookup"><span data-stu-id="91dd1-287">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="91dd1-288">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="91dd1-288">File in the user profile directory</span></span> |

<span data-ttu-id="91dd1-289">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="91dd1-290">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="91dd1-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="91dd1-291">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="91dd1-291">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="91dd1-292">*AppSettings*. `Environment` . *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="91dd1-292">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="91dd1-293">Wpisy tajne użytkownika</span><span class="sxs-lookup"><span data-stu-id="91dd1-293">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="91dd1-294">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-294">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="91dd1-295">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-295">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="91dd1-296">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="91dd1-296">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="91dd1-297">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="91dd1-297">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="91dd1-298">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-298">Connection string prefixes</span></span>

<span data-ttu-id="91dd1-299">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-299">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="91dd1-300">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-300">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="91dd1-301">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-301">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="91dd1-302">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-302">Connection string prefix</span></span> | <span data-ttu-id="91dd1-303">Dostawca</span><span class="sxs-lookup"><span data-stu-id="91dd1-303">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="91dd1-304">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="91dd1-304">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="91dd1-305">MySQL</span><span class="sxs-lookup"><span data-stu-id="91dd1-305">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="91dd1-306">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="91dd1-306">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="91dd1-307">SQL Server</span><span class="sxs-lookup"><span data-stu-id="91dd1-307">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="91dd1-308">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="91dd1-308">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="91dd1-309">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="91dd1-309">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="91dd1-310">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="91dd1-310">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="91dd1-311">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="91dd1-311">Environment variable key</span></span> | <span data-ttu-id="91dd1-312">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-312">Converted configuration key</span></span> | <span data-ttu-id="91dd1-313">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="91dd1-313">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-314">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="91dd1-314">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-315">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-315">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="91dd1-316">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="91dd1-316">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-317">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="91dd1-318">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="91dd1-318">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-319">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="91dd1-320">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="91dd1-320">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="91dd1-321">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-321">File configuration provider</span></span>

<span data-ttu-id="91dd1-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="91dd1-322"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="91dd1-323">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-323">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="91dd1-324">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="91dd1-324">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="91dd1-325">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="91dd1-325">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="91dd1-326">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="91dd1-326">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="91dd1-327">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="91dd1-327">INI configuration provider</span></span>

<span data-ttu-id="91dd1-328"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-328">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="91dd1-329">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-329">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="91dd1-330">W powyższym kodzie ustawienia w *MyIniConfig.ini* i  *MyIniConfig*. `Environment` . pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="91dd1-330">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="91dd1-331">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="91dd1-331">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="91dd1-332">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-332">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="91dd1-333">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="91dd1-333">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="91dd1-334">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-334">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="91dd1-335">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="91dd1-335">JSON configuration provider</span></span>

<span data-ttu-id="91dd1-336"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="91dd1-336">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="91dd1-337">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="91dd1-337">Overloads can specify:</span></span>

* <span data-ttu-id="91dd1-338">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="91dd1-338">Whether the file is optional.</span></span>
* <span data-ttu-id="91dd1-339">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-339">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="91dd1-340">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="91dd1-340">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="91dd1-341">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-341">The preceding code:</span></span>

* <span data-ttu-id="91dd1-342">Konfiguruje dostawcę konfiguracji JSON w celu załadowania *MyConfig.jsw* pliku z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="91dd1-342">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="91dd1-343">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="91dd1-343">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="91dd1-344">`reloadOnChange: true` : Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="91dd1-344">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="91dd1-345">Odczytuje [domyślnych dostawców konfiguracji](#default) przed *MyConfig.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-345">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="91dd1-346">Ustawienia w *MyConfig.js* ustawienia przesłania pliku w domyślnych dostawcach konfiguracji, w tym [dostawca konfiguracji zmiennych środowiskowych](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-346">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="91dd1-347">Zwykle ***nie*** chcesz, aby niestandardowy plik JSON zastępujący wartości ustawione w [zmiennej środowiskowej dostawcy konfiguracji](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-347">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="91dd1-348">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="91dd1-349">W powyższym kodzie ustawienia w *MyConfig.jsna* i  *konfiguracji*. `Environment` .. pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="91dd1-349">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="91dd1-350">Zastąp ustawienia w *appsettings.json* i *AppSettings*. `Environment` .. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-350">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="91dd1-351">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-351">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="91dd1-352">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *MyConfig.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="91dd1-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="91dd1-353">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="91dd1-354">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="91dd1-354">XML configuration provider</span></span>

<span data-ttu-id="91dd1-355"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-355">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="91dd1-356">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-356">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="91dd1-357">W powyższym kodzie ustawienia w *MyXMLFile.xml* i  *MyXMLFile*. `Environment` . pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="91dd1-357">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="91dd1-358">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="91dd1-358">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="91dd1-359">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-359">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="91dd1-360">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="91dd1-360">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="91dd1-361">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-361">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-362">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="91dd1-362">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="91dd1-363">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="91dd1-363">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-364">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="91dd1-364">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="91dd1-365">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-365">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="91dd1-366">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="91dd1-366">key:attribute</span></span>
* <span data-ttu-id="91dd1-367">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="91dd1-367">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="91dd1-368">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-368">Key-per-file configuration provider</span></span>

<span data-ttu-id="91dd1-369"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="91dd1-369">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="91dd1-370">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-370">The key is the file name.</span></span> <span data-ttu-id="91dd1-371">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-371">The value contains the file's contents.</span></span> <span data-ttu-id="91dd1-372">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="91dd1-372">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="91dd1-373">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-373">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="91dd1-374">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="91dd1-374">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="91dd1-375">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="91dd1-376">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="91dd1-376">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="91dd1-377">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-377">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="91dd1-378">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="91dd1-378">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="91dd1-379">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-379">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="91dd1-380">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-380">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="91dd1-381">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="91dd1-381">Memory configuration provider</span></span>

<span data-ttu-id="91dd1-382"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="91dd1-382">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="91dd1-383">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-383">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="91dd1-384">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-384">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-385">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="91dd1-385">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="91dd1-386">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="91dd1-386">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="91dd1-387">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-387">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="91dd1-388">Konfiguracja punktu końcowego Kestrel</span><span class="sxs-lookup"><span data-stu-id="91dd1-388">Kestrel endpoint configuration</span></span>

<span data-ttu-id="91dd1-389">Konfiguracja określonego punktu końcowego Kestrel zastępuje wszystkie konfiguracje punktów końcowych [między serwerami](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-389">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="91dd1-390">Konfiguracje punktu końcowego między serwerami obejmują:</span><span class="sxs-lookup"><span data-stu-id="91dd1-390">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="91dd1-391">UseUrls</span><span class="sxs-lookup"><span data-stu-id="91dd1-391">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="91dd1-392">`--urls` w [wierszu polecenia](xref:fundamentals/configuration/index#command-line)</span><span class="sxs-lookup"><span data-stu-id="91dd1-392">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="91dd1-393">[Zmienna środowiskowa](xref:fundamentals/configuration/index#environment-variables)`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="91dd1-393">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="91dd1-394">Rozważmy następujący *appsettings.json* plik używany w aplikacji internetowej ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="91dd1-394">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="91dd1-395">Gdy poprzednio wyróżnione znaczniki są używane w aplikacji internetowej ASP.NET Core ***i*** aplikacja jest uruchamiana w wierszu polecenia z następującą konfiguracją punktu końcowego między serwerami:</span><span class="sxs-lookup"><span data-stu-id="91dd1-395">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="91dd1-396">Kestrel wiąże się z punktem końcowym skonfigurowanym pod kątem Kestrel w *appsettings.json* pliku ( `https://localhost:9999` ), a nie `https://localhost:7777` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-396">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="91dd1-397">Rozważmy określony punkt końcowy Kestrel skonfigurowany jako zmienna środowiskowa:</span><span class="sxs-lookup"><span data-stu-id="91dd1-397">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="91dd1-398">W poprzedniej zmiennej środowiskowej, `Https` jest nazwą Kestrel określonego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="91dd1-398">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="91dd1-399">Poprzedni *appsettings.json* plik definiuje również określony punkt końcowy Kestrel o nazwie `Https` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-399">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="91dd1-400">[Domyślnie](#default-configuration)zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp) są odczytywane po *appSettings.* `Environment` *. JSON*, w związku z tym, poprzednia zmienna środowiskowa jest używana w `Https` punkcie końcowym.</span><span class="sxs-lookup"><span data-stu-id="91dd1-400">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="91dd1-401">GetValue</span><span class="sxs-lookup"><span data-stu-id="91dd1-401">GetValue</span></span>

<span data-ttu-id="91dd1-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="91dd1-402">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-403">W powyższym kodzie, jeśli `NumberKey` nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="91dd1-403">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="91dd1-404">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="91dd1-404">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="91dd1-405">W poniższych przykładach należy wziąć pod uwagę następujące *MySubsection.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="91dd1-405">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="91dd1-406">Poniższy kod dodaje *MySubsection.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-406">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="91dd1-407">GetSection</span><span class="sxs-lookup"><span data-stu-id="91dd1-407">GetSection</span></span>

<span data-ttu-id="91dd1-408">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-408">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="91dd1-409">Poniższy kod zwraca wartości dla `section1` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-409">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-410">Poniższy kod zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-410">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-411">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-411">`GetSection` never returns `null`.</span></span> <span data-ttu-id="91dd1-412">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="91dd1-412">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="91dd1-413">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="91dd1-413">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="91dd1-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="91dd1-414">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="91dd1-415">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="91dd1-415">GetChildren and Exists</span></span>

<span data-ttu-id="91dd1-416">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-416">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-417">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="91dd1-417">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="91dd1-418">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="91dd1-418">Bind an array</span></span>

<span data-ttu-id="91dd1-419">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-419">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="91dd1-420">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-420">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="91dd1-421">Rozważ *MyArray.jsna* podstawie pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="91dd1-421">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="91dd1-422">Poniższy kod dodaje *MyArray.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-422">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="91dd1-423">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="91dd1-423">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-424">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="91dd1-424">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="91dd1-425">W poprzednich danych wyjściowych indeks 3 ma wartość `value40` odpowiadającą wartości `"4": "value40",` w *MyArray.jsna*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-425">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="91dd1-426">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-426">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="91dd1-427">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="91dd1-427">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="91dd1-428">Poniższy kod ładuje `array:entries` konfigurację z użyciem <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="91dd1-428">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="91dd1-429">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="91dd1-429">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-430">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="91dd1-430">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="91dd1-431">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-431">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="91dd1-432">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-432">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="91dd1-433">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="91dd1-433">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="91dd1-434">Brakujący element konfiguracji dla indeksu &num; 3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje &num; parę klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="91dd1-434">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="91dd1-435">Rozważmy następujące *Value3.js* pliku z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="91dd1-435">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="91dd1-436">Poniższy kod zawiera konfigurację dla *Value3.js* i `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-436">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="91dd1-437">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="91dd1-437">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-438">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="91dd1-438">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="91dd1-439">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-439">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="91dd1-440">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-440">Custom configuration provider</span></span>

<span data-ttu-id="91dd1-441">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="91dd1-441">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="91dd1-442">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="91dd1-442">The provider has the following characteristics:</span></span>

* <span data-ttu-id="91dd1-443">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-443">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="91dd1-444">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-444">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="91dd1-445">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-445">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="91dd1-446">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="91dd1-446">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="91dd1-447">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-447">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="91dd1-448">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-448">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="91dd1-449">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-449">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="91dd1-450">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="91dd1-450">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="91dd1-451">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-451">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="91dd1-452">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-452">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="91dd1-453">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-453">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="91dd1-454">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-454">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="91dd1-455">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="91dd1-455">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="91dd1-456">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="91dd1-456">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="91dd1-457">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-457">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="91dd1-458">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-458">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="91dd1-459">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-459">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="91dd1-460">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-460">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="91dd1-461">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="91dd1-461">Access configuration in Startup</span></span>

<span data-ttu-id="91dd1-462">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="91dd1-462">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="91dd1-463">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="91dd1-463">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="91dd1-464">Konfiguracja dostępu na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="91dd1-464">Access configuration in Razor Pages</span></span>

<span data-ttu-id="91dd1-465">Poniższy kod przedstawia dane konfiguracji na Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-465">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="91dd1-466">W poniższym kodzie `MyOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="91dd1-466">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="91dd1-467">Następujące oznakowanie używa [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy do rozwiązywania i wyświetlania wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-467">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="91dd1-468">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="91dd1-468">Access configuration in a MVC view file</span></span>

<span data-ttu-id="91dd1-469">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="91dd1-469">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="91dd1-470">Konfigurowanie opcji za pomocą delegata</span><span class="sxs-lookup"><span data-stu-id="91dd1-470">Configure options with a delegate</span></span>

<span data-ttu-id="91dd1-471">Opcje skonfigurowane w wartościach zastąpień delegatów ustawionych w dostawcach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-471">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="91dd1-472">Konfigurowanie opcji za pomocą delegata jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-472">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="91dd1-473">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="91dd1-473">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="91dd1-474">Używa delegata do konfigurowania wartości dla `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-474">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="91dd1-475">Poniższy kod wyświetla wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-475">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="91dd1-476">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w, *appsettings.json* a następnie zastąpione przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="91dd1-476">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="91dd1-477">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="91dd1-477">Host versus app configuration</span></span>

<span data-ttu-id="91dd1-478">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="91dd1-478">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="91dd1-479">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-479">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="91dd1-480">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-480">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="91dd1-481">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-481">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="91dd1-482">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-482">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="91dd1-483">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="91dd1-483">Default host configuration</span></span>

<span data-ttu-id="91dd1-484">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](?view=aspnetcore-2.2&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="91dd1-484">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="91dd1-485">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="91dd1-485">Host configuration is provided from:</span></span>
  * <span data-ttu-id="91dd1-486">Zmienne środowiskowe poprzedzone znakiem `DOTNET_` (na przykład `DOTNET_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="91dd1-486">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="91dd1-487">Prefiks ( `DOTNET_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-487">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="91dd1-488">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-488">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="91dd1-489">Konfiguracja domyślna hosta sieci Web ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="91dd1-489">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="91dd1-490">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-490">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="91dd1-491">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="91dd1-491">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="91dd1-492">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` zmienna środowiskowa jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-492">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="91dd1-493">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="91dd1-493">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="91dd1-494">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="91dd1-494">Other configuration</span></span>

<span data-ttu-id="91dd1-495">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-495">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="91dd1-496">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-496">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="91dd1-497">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-497">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="91dd1-498">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-498">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="91dd1-499">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-499">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="91dd1-500">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="91dd1-500">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="91dd1-501">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="91dd1-501">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="91dd1-502">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-502">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="91dd1-503">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="91dd1-503">Add configuration from an external assembly</span></span>

<span data-ttu-id="91dd1-504"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-504">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="91dd1-505">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91dd1-505">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91dd1-506">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="91dd1-506">Additional resources</span></span>

* [<span data-ttu-id="91dd1-507">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-507">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/master/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91dd1-508">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-508">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="91dd1-509">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-509">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="91dd1-510">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91dd1-510">Azure Key Vault</span></span>
* <span data-ttu-id="91dd1-511">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="91dd1-511">Azure App Configuration</span></span>
* <span data-ttu-id="91dd1-512">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-512">Command-line arguments</span></span>
* <span data-ttu-id="91dd1-513">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="91dd1-513">Custom providers (installed or created)</span></span>
* <span data-ttu-id="91dd1-514">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="91dd1-514">Directory files</span></span>
* <span data-ttu-id="91dd1-515">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-515">Environment variables</span></span>
* <span data-ttu-id="91dd1-516">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="91dd1-516">In-memory .NET objects</span></span>
* <span data-ttu-id="91dd1-517">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="91dd1-517">Settings files</span></span>

<span data-ttu-id="91dd1-518">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="91dd1-518">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="91dd1-519">Przykłady kodu, które obserwują i w przykładowej aplikacji używają <xref:Microsoft.Extensions.Configuration> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="91dd1-519">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="91dd1-520">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-520">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="91dd1-521">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="91dd1-521">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="91dd1-522">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="91dd1-522">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="91dd1-523">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91dd1-523">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="91dd1-524">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="91dd1-524">Host versus app configuration</span></span>

<span data-ttu-id="91dd1-525">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="91dd1-525">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="91dd1-526">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-526">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="91dd1-527">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-527">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="91dd1-528">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-528">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="91dd1-529">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-529">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="91dd1-530">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="91dd1-530">Other configuration</span></span>

<span data-ttu-id="91dd1-531">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-531">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="91dd1-532">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-532">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="91dd1-533">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-533">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="91dd1-534">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-534">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="91dd1-535">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-535">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="91dd1-536">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="91dd1-536">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="91dd1-537">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-537">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="91dd1-538">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="91dd1-538">Default configuration</span></span>

<span data-ttu-id="91dd1-539">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych szablonów dotnet](/dotnet/core/tools/dotnet-new) są wywoływane <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="91dd1-539">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="91dd1-540">`CreateDefaultBuilder` zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="91dd1-540">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="91dd1-541">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="91dd1-541">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="91dd1-542">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="91dd1-542">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="91dd1-543">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="91dd1-543">Host configuration is provided from:</span></span>
  * <span data-ttu-id="91dd1-544">Zmienne środowiskowe poprzedzone znakiem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-544">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="91dd1-545">Prefiks ( `ASPNETCORE_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-545">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="91dd1-546">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-546">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="91dd1-547">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="91dd1-547">App configuration is provided from:</span></span>
  * <span data-ttu-id="91dd1-548">*appsettings.json* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-548">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="91dd1-549">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-549">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="91dd1-550">[Klucze tajne użytkownika](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="91dd1-550">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="91dd1-551">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-551">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="91dd1-552">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="91dd1-552">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="91dd1-553">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-553">Security</span></span>

<span data-ttu-id="91dd1-554">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="91dd1-554">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="91dd1-555">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-555">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="91dd1-556">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-556">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="91dd1-557">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="91dd1-557">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="91dd1-558">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="91dd1-558">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="91dd1-559"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-559"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="91dd1-560">Narzędzie Secret Manager używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="91dd1-560">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="91dd1-561">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-561">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="91dd1-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-562">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="91dd1-563">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91dd1-563">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="91dd1-564">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-564">Hierarchical configuration data</span></span>

<span data-ttu-id="91dd1-565">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-565">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="91dd1-566">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-566">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="91dd1-567">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-567">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="91dd1-568">Sekcje i klucze są spłaszczone przy użyciu dwukropka (), `:` Aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="91dd1-568">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="91dd1-569">section0:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-569">section0:key0</span></span>
* <span data-ttu-id="91dd1-570">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-570">section0:key1</span></span>
* <span data-ttu-id="91dd1-571">section1:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-571">section1:key0</span></span>
* <span data-ttu-id="91dd1-572">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-572">section1:key1</span></span>

<span data-ttu-id="91dd1-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-573"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="91dd1-574">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="91dd1-574">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="91dd1-575">Konwencje</span><span class="sxs-lookup"><span data-stu-id="91dd1-575">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="91dd1-576">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="91dd1-576">Sources and providers</span></span>

<span data-ttu-id="91dd1-577">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-577">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="91dd1-578">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="91dd1-578">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="91dd1-579">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="91dd1-579">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="91dd1-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-580"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="91dd1-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> można wstrzyknąć do Razor stron <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC, <xref:Microsoft.AspNetCore.Mvc.Controller> Aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-581"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="91dd1-582">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="91dd1-582">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="91dd1-583">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="91dd1-583">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="91dd1-584">Klucze</span><span class="sxs-lookup"><span data-stu-id="91dd1-584">Keys</span></span>

<span data-ttu-id="91dd1-585">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="91dd1-585">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="91dd1-586">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="91dd1-586">Keys are case-insensitive.</span></span> <span data-ttu-id="91dd1-587">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="91dd1-587">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="91dd1-588">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="91dd1-588">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="91dd1-589">Aby uzyskać więcej informacji na temat zduplikowanych kluczy JSON, zobacz [ten problem](https://github.com/dotnet/extensions/issues/2381)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="91dd1-589">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="91dd1-590">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="91dd1-590">Hierarchical keys</span></span>
  * <span data-ttu-id="91dd1-591">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="91dd1-591">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="91dd1-592">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="91dd1-592">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="91dd1-593">Podwójne podkreślenie ( `__` ) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="91dd1-593">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="91dd1-594">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="91dd1-594">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="91dd1-595">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-595">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="91dd1-596"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-596">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="91dd1-597">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-597">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="91dd1-598">Wartości</span><span class="sxs-lookup"><span data-stu-id="91dd1-598">Values</span></span>

<span data-ttu-id="91dd1-599">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="91dd1-599">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="91dd1-600">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="91dd1-600">Values are strings.</span></span>
* <span data-ttu-id="91dd1-601">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="91dd1-601">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="91dd1-602">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="91dd1-602">Providers</span></span>

<span data-ttu-id="91dd1-603">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-603">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="91dd1-604">Dostawca</span><span class="sxs-lookup"><span data-stu-id="91dd1-604">Provider</span></span> | <span data-ttu-id="91dd1-605">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="91dd1-605">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="91dd1-606">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące *zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="91dd1-606">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="91dd1-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91dd1-607">Azure Key Vault</span></span> |
| <span data-ttu-id="91dd1-608">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="91dd1-608">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="91dd1-609">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="91dd1-609">Azure App Configuration</span></span> |
| [<span data-ttu-id="91dd1-610">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-610">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="91dd1-611">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-611">Command-line parameters</span></span> |
| [<span data-ttu-id="91dd1-612">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-612">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="91dd1-613">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-613">Custom source</span></span> |
| [<span data-ttu-id="91dd1-614">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="91dd1-614">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="91dd1-615">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-615">Environment variables</span></span> |
| [<span data-ttu-id="91dd1-616">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-616">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="91dd1-617">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="91dd1-617">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="91dd1-618">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-618">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="91dd1-619">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="91dd1-619">Directory files</span></span> |
| [<span data-ttu-id="91dd1-620">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="91dd1-620">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="91dd1-621">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="91dd1-621">In-memory collections</span></span> |
| <span data-ttu-id="91dd1-622">Wpisy [tajne użytkownika](xref:security/app-secrets) (tematy dotyczące *zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="91dd1-622">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="91dd1-623">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="91dd1-623">File in the user profile directory</span></span> |

<span data-ttu-id="91dd1-624">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-624">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="91dd1-625">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="91dd1-625">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="91dd1-626">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="91dd1-626">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="91dd1-627">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="91dd1-627">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="91dd1-628">Pliki ( *appsettings.json* , *appSettings. { Environment}. JSON*, gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="91dd1-628">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="91dd1-629">Usługa Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91dd1-629">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="91dd1-630">Wpisy [tajne użytkownika](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="91dd1-630">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="91dd1-631">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="91dd1-631">Environment variables</span></span>
1. <span data-ttu-id="91dd1-632">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-632">Command-line arguments</span></span>

<span data-ttu-id="91dd1-633">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="91dd1-633">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="91dd1-634">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu programu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-634">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91dd1-635">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-635">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="91dd1-636">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="91dd1-636">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="91dd1-637">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="91dd1-637">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="91dd1-638">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="91dd1-638">ConfigureAppConfiguration</span></span>

<span data-ttu-id="91dd1-639">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-639">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="91dd1-640">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-640">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="91dd1-641">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="91dd1-641">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="91dd1-642">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="91dd1-642">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="91dd1-643">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder` , najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="91dd1-643">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="91dd1-644">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="91dd1-644">Consume configuration during app startup</span></span>

<span data-ttu-id="91dd1-645">Konfiguracja dostarczona do aplikacji w programie `ConfigureAppConfiguration` jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-645">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="91dd1-646">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-646">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="91dd1-647">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-647">Command-line Configuration Provider</span></span>

<span data-ttu-id="91dd1-648"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-648">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="91dd1-649">Aby uaktywnić konfigurację wiersza polecenia, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-649">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="91dd1-650">`AddCommandLine` jest wywoływana automatycznie, gdy `CreateDefaultBuilder(string [])` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="91dd1-650">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="91dd1-651">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-651">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="91dd1-652">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="91dd1-652">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="91dd1-653">Opcjonalna konfiguracja z *appsettings.json* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="91dd1-653">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="91dd1-654">Wpisy [tajne użytkownika](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="91dd1-654">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="91dd1-655">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-655">Environment variables.</span></span>

<span data-ttu-id="91dd1-656">`CreateDefaultBuilder` dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="91dd1-656">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="91dd1-657">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="91dd1-657">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="91dd1-658">`CreateDefaultBuilder` działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="91dd1-658">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="91dd1-659">W związku z tym konfiguracja wiersza polecenia aktywowana przez program `CreateDefaultBuilder` może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="91dd1-659">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="91dd1-660">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-660">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91dd1-661">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj jako `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="91dd1-661">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="91dd1-662">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="91dd1-662">**Example**</span></span>

<span data-ttu-id="91dd1-663">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-663">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="91dd1-664">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-664">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="91dd1-665">Podaj do polecenia argument wiersza polecenia `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-665">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="91dd1-666">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-666">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="91dd1-667">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-667">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="91dd1-668">Argumenty</span><span class="sxs-lookup"><span data-stu-id="91dd1-668">Arguments</span></span>

<span data-ttu-id="91dd1-669">Wartość musi następować po znaku równości ( `=` ) lub klucz musi mieć prefiks ( `--` lub `/` ), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-669">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="91dd1-670">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="91dd1-670">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="91dd1-671">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="91dd1-671">Key prefix</span></span>               | <span data-ttu-id="91dd1-672">Przykład</span><span class="sxs-lookup"><span data-stu-id="91dd1-672">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="91dd1-673">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="91dd1-673">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="91dd1-674">Dwie kreski ( `--` )</span><span class="sxs-lookup"><span data-stu-id="91dd1-674">Two dashes (`--`)</span></span>        | <span data-ttu-id="91dd1-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="91dd1-675">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="91dd1-676">Ukośnik ( `/` )</span><span class="sxs-lookup"><span data-stu-id="91dd1-676">Forward slash (`/`)</span></span>      | <span data-ttu-id="91dd1-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="91dd1-677">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="91dd1-678">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-678">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="91dd1-679">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="91dd1-679">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="91dd1-680">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="91dd1-680">Switch mappings</span></span>

<span data-ttu-id="91dd1-681">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="91dd1-681">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="91dd1-682">Podczas ręcznego kompilowania konfiguracji za pomocą programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="91dd1-682">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="91dd1-683">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-683">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="91dd1-684">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-684">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="91dd1-685">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="91dd1-685">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="91dd1-686">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="91dd1-686">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="91dd1-687">Przełączniki muszą zaczynać się kreską ( `-` ) lub podwójną kreską ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="91dd1-687">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="91dd1-688">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-688">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="91dd1-689">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-689">Create a switch mappings dictionary.</span></span> <span data-ttu-id="91dd1-690">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="91dd1-690">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="91dd1-691">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="91dd1-691">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="91dd1-692">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="91dd1-692">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="91dd1-693">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-693">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91dd1-694">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="91dd1-694">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="91dd1-695">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="91dd1-695">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="91dd1-696">Klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-696">Key</span></span>       | <span data-ttu-id="91dd1-697">Wartość</span><span class="sxs-lookup"><span data-stu-id="91dd1-697">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="91dd1-698">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="91dd1-698">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="91dd1-699">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="91dd1-699">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="91dd1-700">Klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-700">Key</span></span>               | <span data-ttu-id="91dd1-701">Wartość</span><span class="sxs-lookup"><span data-stu-id="91dd1-701">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="91dd1-702">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="91dd1-702">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="91dd1-703"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-703">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="91dd1-704">Aby uaktywnić konfigurację zmiennych środowiskowych, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-704">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="91dd1-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-705">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="91dd1-706">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="91dd1-706">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="91dd1-707">`AddEnvironmentVariables` służy do ładowania zmiennych środowiskowych, które są poprzedzone `ASPNETCORE_` [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="91dd1-707">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="91dd1-708">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-708">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="91dd1-709">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="91dd1-709">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="91dd1-710">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez wywołanie `AddEnvironmentVariables` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-710">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="91dd1-711">Opcjonalna konfiguracja z *appsettings.json* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="91dd1-711">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="91dd1-712">Wpisy [tajne użytkownika](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="91dd1-712">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="91dd1-713">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-713">Command-line arguments.</span></span>

<span data-ttu-id="91dd1-714">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-714">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="91dd1-715">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-715">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="91dd1-716">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddEnvironmentVariables` z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="91dd1-716">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="91dd1-717">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="91dd1-717">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="91dd1-718">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="91dd1-718">**Example**</span></span>

<span data-ttu-id="91dd1-719">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-719">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="91dd1-720">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="91dd1-720">Run the sample app.</span></span> <span data-ttu-id="91dd1-721">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-721">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="91dd1-722">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej środowiskowej `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-722">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="91dd1-723">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj w `Development` przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="91dd1-723">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="91dd1-724">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-724">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="91dd1-725">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-725">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="91dd1-726">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić `FilteredConfiguration` stronę na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="91dd1-726">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="91dd1-727">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="91dd1-727">Prefixes</span></span>

<span data-ttu-id="91dd1-728">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="91dd1-728">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="91dd1-729">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_` , podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-729">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="91dd1-730">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-730">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="91dd1-731">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-731">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="91dd1-732">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-732">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="91dd1-733">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="91dd1-733">**Connection string prefixes**</span></span>

<span data-ttu-id="91dd1-734">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-734">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="91dd1-735">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-735">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="91dd1-736">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="91dd1-736">Connection string prefix</span></span> | <span data-ttu-id="91dd1-737">Dostawca</span><span class="sxs-lookup"><span data-stu-id="91dd1-737">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="91dd1-738">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="91dd1-738">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="91dd1-739">MySQL</span><span class="sxs-lookup"><span data-stu-id="91dd1-739">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="91dd1-740">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="91dd1-740">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="91dd1-741">SQL Server</span><span class="sxs-lookup"><span data-stu-id="91dd1-741">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="91dd1-742">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="91dd1-742">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="91dd1-743">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="91dd1-743">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="91dd1-744">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="91dd1-744">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="91dd1-745">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="91dd1-745">Environment variable key</span></span> | <span data-ttu-id="91dd1-746">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-746">Converted configuration key</span></span> | <span data-ttu-id="91dd1-747">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="91dd1-747">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-748">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="91dd1-748">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-749">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-749">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="91dd1-750">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="91dd1-750">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-751">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="91dd1-752">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="91dd1-752">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="91dd1-753">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="91dd1-754">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="91dd1-754">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="91dd1-755">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="91dd1-755">**Example**</span></span>

<span data-ttu-id="91dd1-756">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="91dd1-756">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="91dd1-757">Nazwa: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="91dd1-757">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="91dd1-758">Wartość: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="91dd1-758">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="91dd1-759">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config` , odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="91dd1-759">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="91dd1-760">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-760">File Configuration Provider</span></span>

<span data-ttu-id="91dd1-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="91dd1-761"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="91dd1-762">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="91dd1-762">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="91dd1-763">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="91dd1-763">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="91dd1-764">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="91dd1-764">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="91dd1-765">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="91dd1-765">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="91dd1-766">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="91dd1-766">INI Configuration Provider</span></span>

<span data-ttu-id="91dd1-767"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-767">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="91dd1-768">Aby uaktywnić konfigurację pliku INI, wywołaj <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-768">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="91dd1-769">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="91dd1-769">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="91dd1-770">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-770">Overloads permit specifying:</span></span>

* <span data-ttu-id="91dd1-771">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="91dd1-771">Whether the file is optional.</span></span>
* <span data-ttu-id="91dd1-772">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-772">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="91dd1-773"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-773">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="91dd1-774">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="91dd1-775">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="91dd1-775">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="91dd1-776">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-776">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="91dd1-777">section0:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-777">section0:key0</span></span>
* <span data-ttu-id="91dd1-778">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-778">section0:key1</span></span>
* <span data-ttu-id="91dd1-779">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="91dd1-779">section1:subsection:key</span></span>
* <span data-ttu-id="91dd1-780">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-780">section2:subsection0:key</span></span>
* <span data-ttu-id="91dd1-781">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-781">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="91dd1-782">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="91dd1-782">JSON Configuration Provider</span></span>

<span data-ttu-id="91dd1-783"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="91dd1-783">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="91dd1-784">Aby uaktywnić konfigurację pliku JSON, wywołaj <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-784">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="91dd1-785">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-785">Overloads permit specifying:</span></span>

* <span data-ttu-id="91dd1-786">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="91dd1-786">Whether the file is optional.</span></span>
* <span data-ttu-id="91dd1-787">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-787">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="91dd1-788"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-788">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="91dd1-789">`AddJsonFile` jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-789">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="91dd1-790">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="91dd1-790">The method is called to load configuration from:</span></span>

* <span data-ttu-id="91dd1-791">*appsettings.json*: Ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-791">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="91dd1-792">Wersja środowiska pliku może przesłonić wartości dostarczone przez *appsettings.json* plik.</span><span class="sxs-lookup"><span data-stu-id="91dd1-792">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="91dd1-793">*appSettings. {Environment}. JSON*: wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="91dd1-793">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="91dd1-794">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="91dd1-794">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="91dd1-795">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="91dd1-795">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="91dd1-796">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="91dd1-796">Environment variables.</span></span>
* <span data-ttu-id="91dd1-797">Wpisy [tajne użytkownika](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="91dd1-797">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="91dd1-798">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="91dd1-798">Command-line arguments.</span></span>

<span data-ttu-id="91dd1-799">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-799">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="91dd1-800">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-800">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="91dd1-801">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *appsettings.json* i *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="91dd1-802">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="91dd1-802">**Example**</span></span>

<span data-ttu-id="91dd1-803">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-803">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="91dd1-804">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="91dd1-804">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="91dd1-805">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="91dd1-805">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="91dd1-806">W przypadku *appsettings.Development.js* w aplikacji przykładowej załadowano następujący plik:</span><span class="sxs-lookup"><span data-stu-id="91dd1-806">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="91dd1-807">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="91dd1-807">Run the sample app.</span></span> <span data-ttu-id="91dd1-808">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-808">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="91dd1-809">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-809">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="91dd1-810">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="91dd1-810">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="91dd1-811">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="91dd1-811">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="91dd1-812">Otwórz *Właściwości/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-812">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="91dd1-813">W `ConfigurationSample` profilu Zmień wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej na `Production` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-813">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="91dd1-814">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="91dd1-814">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="91dd1-815">Ustawienia w *appsettings.Development.js* nie przesłaniają już ustawień w programie *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="91dd1-815">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="91dd1-816">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-816">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="91dd1-817">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="91dd1-817">XML Configuration Provider</span></span>

<span data-ttu-id="91dd1-818"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-818">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="91dd1-819">Aby uaktywnić konfigurację pliku XML, wywołaj <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-819">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="91dd1-820">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-820">Overloads permit specifying:</span></span>

* <span data-ttu-id="91dd1-821">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="91dd1-821">Whether the file is optional.</span></span>
* <span data-ttu-id="91dd1-822">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-822">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="91dd1-823"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-823">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="91dd1-824">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-824">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="91dd1-825">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-825">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="91dd1-826">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-826">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="91dd1-827">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-827">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="91dd1-828">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-828">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="91dd1-829">section0:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-829">section0:key0</span></span>
* <span data-ttu-id="91dd1-830">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-830">section0:key1</span></span>
* <span data-ttu-id="91dd1-831">section1:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-831">section1:key0</span></span>
* <span data-ttu-id="91dd1-832">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-832">section1:key1</span></span>

<span data-ttu-id="91dd1-833">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="91dd1-833">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="91dd1-834">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-834">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="91dd1-835">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-835">section:section0:key:key0</span></span>
* <span data-ttu-id="91dd1-836">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-836">section:section0:key:key1</span></span>
* <span data-ttu-id="91dd1-837">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-837">section:section1:key:key0</span></span>
* <span data-ttu-id="91dd1-838">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-838">section:section1:key:key1</span></span>

<span data-ttu-id="91dd1-839">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="91dd1-839">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="91dd1-840">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="91dd1-840">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="91dd1-841">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="91dd1-841">key:attribute</span></span>
* <span data-ttu-id="91dd1-842">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="91dd1-842">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="91dd1-843">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="91dd1-843">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="91dd1-844"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="91dd1-844">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="91dd1-845">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-845">The key is the file name.</span></span> <span data-ttu-id="91dd1-846">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-846">The value contains the file's contents.</span></span> <span data-ttu-id="91dd1-847">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="91dd1-847">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="91dd1-848">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-848">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="91dd1-849">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="91dd1-849">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="91dd1-850">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-850">Overloads permit specifying:</span></span>

* <span data-ttu-id="91dd1-851">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="91dd1-851">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="91dd1-852">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-852">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="91dd1-853">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="91dd1-853">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="91dd1-854">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-854">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="91dd1-855">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-855">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="91dd1-856">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="91dd1-856">Memory Configuration Provider</span></span>

<span data-ttu-id="91dd1-857"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="91dd1-857">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="91dd1-858">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-858">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="91dd1-859">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-859">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="91dd1-860">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-860">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="91dd1-861">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-861">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="91dd1-862">Słownik jest używany z wywołaniem do `AddInMemoryCollection` zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-862">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="91dd1-863">GetValue</span><span class="sxs-lookup"><span data-stu-id="91dd1-863">GetValue</span></span>

<span data-ttu-id="91dd1-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="91dd1-864">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="91dd1-865">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="91dd1-865">An overload accepts a default value.</span></span>

<span data-ttu-id="91dd1-866">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="91dd1-866">The following example:</span></span>

* <span data-ttu-id="91dd1-867">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-867">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="91dd1-868">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="91dd1-868">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="91dd1-869">Typ wartości `int` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-869">Types the value as an `int`.</span></span>
* <span data-ttu-id="91dd1-870">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="91dd1-870">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="91dd1-871">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="91dd1-871">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="91dd1-872">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="91dd1-872">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="91dd1-873">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-873">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="91dd1-874">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="91dd1-874">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="91dd1-875">section0:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-875">section0:key0</span></span>
* <span data-ttu-id="91dd1-876">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-876">section0:key1</span></span>
* <span data-ttu-id="91dd1-877">section1:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-877">section1:key0</span></span>
* <span data-ttu-id="91dd1-878">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-878">section1:key1</span></span>
* <span data-ttu-id="91dd1-879">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-879">section2:subsection0:key0</span></span>
* <span data-ttu-id="91dd1-880">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-880">section2:subsection0:key1</span></span>
* <span data-ttu-id="91dd1-881">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="91dd1-881">section2:subsection1:key0</span></span>
* <span data-ttu-id="91dd1-882">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="91dd1-882">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="91dd1-883">GetSection</span><span class="sxs-lookup"><span data-stu-id="91dd1-883">GetSection</span></span>

<span data-ttu-id="91dd1-884">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-884">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="91dd1-885">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1` , wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-885">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="91dd1-886">`configSection`Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="91dd1-886">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="91dd1-887">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0` , wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-887">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="91dd1-888">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-888">`GetSection` never returns `null`.</span></span> <span data-ttu-id="91dd1-889">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="91dd1-889">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="91dd1-890">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="91dd1-890">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="91dd1-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="91dd1-891">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="91dd1-892">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="91dd1-892">GetChildren</span></span>

<span data-ttu-id="91dd1-893">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w programie `section2` uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="91dd1-893">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="91dd1-894">Exists</span><span class="sxs-lookup"><span data-stu-id="91dd1-894">Exists</span></span>

<span data-ttu-id="91dd1-895">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-895">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="91dd1-896">Dane przykładowe są spowodowane tym, że `sectionExists` `false` `section2:subsection2` w danych konfiguracji nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-896">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="91dd1-897">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="91dd1-897">Bind to an object graph</span></span>

<span data-ttu-id="91dd1-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="91dd1-898"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="91dd1-899">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-899">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="91dd1-900">Przykład zawiera `TvShow` model, którego obiekt zawiera obiekty `Metadata` i `Actors` klasy (*modele/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="91dd1-900">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="91dd1-901">Przykładowa aplikacja zawiera plik *tvshow.xml* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-901">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="91dd1-902">Konfiguracja jest powiązana z `TvShow` wykresem całego obiektu za pomocą `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="91dd1-902">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="91dd1-903">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="91dd1-903">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="91dd1-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="91dd1-904">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="91dd1-905">`Get<T>` jest wygodniejszy niż używanie `Bind` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-905">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="91dd1-906">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="91dd1-906">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="91dd1-907">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="91dd1-907">Bind an array to a class</span></span>

<span data-ttu-id="91dd1-908">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="91dd1-908">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="91dd1-909"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-909">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="91dd1-910">Każdy format tablicy, który ujawnia segment klucza numerycznego ( `:0:` , `:1:` , &hellip; `:{n}:` ) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="91dd1-910">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="91dd1-911">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-911">Binding is provided by convention.</span></span> <span data-ttu-id="91dd1-912">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="91dd1-912">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="91dd1-913">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="91dd1-913">**In-memory array processing**</span></span>

<span data-ttu-id="91dd1-914">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="91dd1-914">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="91dd1-915">Klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-915">Key</span></span>             | <span data-ttu-id="91dd1-916">Wartość</span><span class="sxs-lookup"><span data-stu-id="91dd1-916">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="91dd1-917">Tablica: wpisy: 0</span><span class="sxs-lookup"><span data-stu-id="91dd1-917">array:entries:0</span></span> | <span data-ttu-id="91dd1-918">value0</span><span class="sxs-lookup"><span data-stu-id="91dd1-918">value0</span></span> |
| <span data-ttu-id="91dd1-919">Tablica: wpisy: 1</span><span class="sxs-lookup"><span data-stu-id="91dd1-919">array:entries:1</span></span> | <span data-ttu-id="91dd1-920">sekwencj</span><span class="sxs-lookup"><span data-stu-id="91dd1-920">value1</span></span> |
| <span data-ttu-id="91dd1-921">Tablica: wpisy: 2</span><span class="sxs-lookup"><span data-stu-id="91dd1-921">array:entries:2</span></span> | <span data-ttu-id="91dd1-922">wartość2</span><span class="sxs-lookup"><span data-stu-id="91dd1-922">value2</span></span> |
| <span data-ttu-id="91dd1-923">Tablica: wpisy: 4</span><span class="sxs-lookup"><span data-stu-id="91dd1-923">array:entries:4</span></span> | <span data-ttu-id="91dd1-924">value4</span><span class="sxs-lookup"><span data-stu-id="91dd1-924">value4</span></span> |
| <span data-ttu-id="91dd1-925">Tablica: wpisy: 5</span><span class="sxs-lookup"><span data-stu-id="91dd1-925">array:entries:5</span></span> | <span data-ttu-id="91dd1-926">value5</span><span class="sxs-lookup"><span data-stu-id="91dd1-926">value5</span></span> |

<span data-ttu-id="91dd1-927">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="91dd1-927">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="91dd1-928">Tablica pomija wartość dla indeksu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="91dd1-928">The array skips a value for index &num;3.</span></span> <span data-ttu-id="91dd1-929">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="91dd1-929">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="91dd1-930">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-930">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="91dd1-931">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="91dd1-931">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="91dd1-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="91dd1-932">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="91dd1-933">Obiekt powiązany, wystąpienie elementu `ArrayExample` , otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-933">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="91dd1-934">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="91dd1-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="91dd1-935">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="91dd1-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="91dd1-936">0</span><span class="sxs-lookup"><span data-stu-id="91dd1-936">0</span></span>                            | <span data-ttu-id="91dd1-937">value0</span><span class="sxs-lookup"><span data-stu-id="91dd1-937">value0</span></span>                       |
| <span data-ttu-id="91dd1-938">1</span><span class="sxs-lookup"><span data-stu-id="91dd1-938">1</span></span>                            | <span data-ttu-id="91dd1-939">sekwencj</span><span class="sxs-lookup"><span data-stu-id="91dd1-939">value1</span></span>                       |
| <span data-ttu-id="91dd1-940">2</span><span class="sxs-lookup"><span data-stu-id="91dd1-940">2</span></span>                            | <span data-ttu-id="91dd1-941">wartość2</span><span class="sxs-lookup"><span data-stu-id="91dd1-941">value2</span></span>                       |
| <span data-ttu-id="91dd1-942">3</span><span class="sxs-lookup"><span data-stu-id="91dd1-942">3</span></span>                            | <span data-ttu-id="91dd1-943">value4</span><span class="sxs-lookup"><span data-stu-id="91dd1-943">value4</span></span>                       |
| <span data-ttu-id="91dd1-944">4</span><span class="sxs-lookup"><span data-stu-id="91dd1-944">4</span></span>                            | <span data-ttu-id="91dd1-945">value5</span><span class="sxs-lookup"><span data-stu-id="91dd1-945">value5</span></span>                       |

<span data-ttu-id="91dd1-946">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-946">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="91dd1-947">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="91dd1-947">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="91dd1-948">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="91dd1-948">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="91dd1-949">Brakujący element konfiguracji dla indeksu &num; 3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-949">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="91dd1-950">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="91dd1-950">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="91dd1-951">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-951">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="91dd1-952">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="91dd1-952">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="91dd1-953">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-953">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="91dd1-954">Klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-954">Key</span></span>             | <span data-ttu-id="91dd1-955">Wartość</span><span class="sxs-lookup"><span data-stu-id="91dd1-955">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="91dd1-956">Tablica: wpisy: 3</span><span class="sxs-lookup"><span data-stu-id="91dd1-956">array:entries:3</span></span> | <span data-ttu-id="91dd1-957">Wartość3</span><span class="sxs-lookup"><span data-stu-id="91dd1-957">value3</span></span> |

<span data-ttu-id="91dd1-958">Jeśli `ArrayExample` wystąpienie klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num; 3, `ArrayExample.Entries` Tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="91dd1-958">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="91dd1-959">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="91dd1-959">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="91dd1-960">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="91dd1-960">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="91dd1-961">0</span><span class="sxs-lookup"><span data-stu-id="91dd1-961">0</span></span>                            | <span data-ttu-id="91dd1-962">value0</span><span class="sxs-lookup"><span data-stu-id="91dd1-962">value0</span></span>                       |
| <span data-ttu-id="91dd1-963">1</span><span class="sxs-lookup"><span data-stu-id="91dd1-963">1</span></span>                            | <span data-ttu-id="91dd1-964">sekwencj</span><span class="sxs-lookup"><span data-stu-id="91dd1-964">value1</span></span>                       |
| <span data-ttu-id="91dd1-965">2</span><span class="sxs-lookup"><span data-stu-id="91dd1-965">2</span></span>                            | <span data-ttu-id="91dd1-966">wartość2</span><span class="sxs-lookup"><span data-stu-id="91dd1-966">value2</span></span>                       |
| <span data-ttu-id="91dd1-967">3</span><span class="sxs-lookup"><span data-stu-id="91dd1-967">3</span></span>                            | <span data-ttu-id="91dd1-968">Wartość3</span><span class="sxs-lookup"><span data-stu-id="91dd1-968">value3</span></span>                       |
| <span data-ttu-id="91dd1-969">4</span><span class="sxs-lookup"><span data-stu-id="91dd1-969">4</span></span>                            | <span data-ttu-id="91dd1-970">value4</span><span class="sxs-lookup"><span data-stu-id="91dd1-970">value4</span></span>                       |
| <span data-ttu-id="91dd1-971">5</span><span class="sxs-lookup"><span data-stu-id="91dd1-971">5</span></span>                            | <span data-ttu-id="91dd1-972">value5</span><span class="sxs-lookup"><span data-stu-id="91dd1-972">value5</span></span>                       |

<span data-ttu-id="91dd1-973">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="91dd1-973">**JSON array processing**</span></span>

<span data-ttu-id="91dd1-974">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="91dd1-974">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="91dd1-975">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="91dd1-975">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="91dd1-976">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="91dd1-976">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="91dd1-977">Klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-977">Key</span></span>                     | <span data-ttu-id="91dd1-978">Wartość</span><span class="sxs-lookup"><span data-stu-id="91dd1-978">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="91dd1-979">json_array: klucz</span><span class="sxs-lookup"><span data-stu-id="91dd1-979">json_array:key</span></span>          | <span data-ttu-id="91dd1-980">wartośća</span><span class="sxs-lookup"><span data-stu-id="91dd1-980">valueA</span></span> |
| <span data-ttu-id="91dd1-981">json_array: podsekcja: 0</span><span class="sxs-lookup"><span data-stu-id="91dd1-981">json_array:subsection:0</span></span> | <span data-ttu-id="91dd1-982">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="91dd1-982">valueB</span></span> |
| <span data-ttu-id="91dd1-983">json_array: podsekcja: 1</span><span class="sxs-lookup"><span data-stu-id="91dd1-983">json_array:subsection:1</span></span> | <span data-ttu-id="91dd1-984">valueC</span><span class="sxs-lookup"><span data-stu-id="91dd1-984">valueC</span></span> |
| <span data-ttu-id="91dd1-985">json_array: podsekcja: 2</span><span class="sxs-lookup"><span data-stu-id="91dd1-985">json_array:subsection:2</span></span> | <span data-ttu-id="91dd1-986">Znajdując</span><span class="sxs-lookup"><span data-stu-id="91dd1-986">valueD</span></span> |

<span data-ttu-id="91dd1-987">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="91dd1-987">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="91dd1-988">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-988">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="91dd1-989">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-989">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="91dd1-990">`JsonArrayExample.Subsection` Indeks</span><span class="sxs-lookup"><span data-stu-id="91dd1-990">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="91dd1-991">`JsonArrayExample.Subsection` Wartościami</span><span class="sxs-lookup"><span data-stu-id="91dd1-991">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="91dd1-992">0</span><span class="sxs-lookup"><span data-stu-id="91dd1-992">0</span></span>                                   | <span data-ttu-id="91dd1-993">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="91dd1-993">valueB</span></span>                              |
| <span data-ttu-id="91dd1-994">1</span><span class="sxs-lookup"><span data-stu-id="91dd1-994">1</span></span>                                   | <span data-ttu-id="91dd1-995">valueC</span><span class="sxs-lookup"><span data-stu-id="91dd1-995">valueC</span></span>                              |
| <span data-ttu-id="91dd1-996">2</span><span class="sxs-lookup"><span data-stu-id="91dd1-996">2</span></span>                                   | <span data-ttu-id="91dd1-997">Znajdując</span><span class="sxs-lookup"><span data-stu-id="91dd1-997">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="91dd1-998">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="91dd1-998">Custom configuration provider</span></span>

<span data-ttu-id="91dd1-999">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="91dd1-999">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="91dd1-1000">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1000">The provider has the following characteristics:</span></span>

* <span data-ttu-id="91dd1-1001">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1001">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="91dd1-1002">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1002">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="91dd1-1003">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1003">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="91dd1-1004">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1004">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="91dd1-1005">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1005">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="91dd1-1006">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1006">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="91dd1-1007">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1007">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="91dd1-1008">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1008">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="91dd1-1009">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1009">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="91dd1-1010">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-1010">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="91dd1-1011">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1011">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="91dd1-1012">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="91dd1-1012">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="91dd1-1013">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1013">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="91dd1-1014">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1014">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="91dd1-1015">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-1015">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="91dd1-1016">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1016">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="91dd1-1017">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1017">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="91dd1-1018">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="91dd1-1018">Access configuration during startup</span></span>

<span data-ttu-id="91dd1-1019">Wsuń `IConfiguration` do `Startup` konstruktora, aby uzyskać dostęp do wartości konfiguracyjnych w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="91dd1-1019">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="91dd1-1020">Aby uzyskać dostęp do konfiguracji w programie `Startup.Configure` , należy wstrzyknąć `IConfiguration` bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1020">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="91dd1-1021">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="91dd1-1021">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="91dd1-1022">Konfiguracja dostępu na Razor stronie stron lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="91dd1-1022">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="91dd1-1023">Aby uzyskać dostęp do ustawień konfiguracji na Razor stronie stron lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [ przestrzeni nazwMicrosoft.Extensions.Configwersja](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1023">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="91dd1-1024">Na Razor stronie stron:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1024">In a Razor Pages page:</span></span>

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

<span data-ttu-id="91dd1-1025">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="91dd1-1025">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="91dd1-1026">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="91dd1-1026">Add configuration from an external assembly</span></span>

<span data-ttu-id="91dd1-1027"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1027">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="91dd1-1028">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="91dd1-1028">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91dd1-1029">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="91dd1-1029">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
