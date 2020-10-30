---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core przy użyciu interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/configuration/index
ms.openlocfilehash: 9e744ec6d0f0dd72bded8284e98fd9ce53056b84
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057976"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="dd6ad-103">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd6ad-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="dd6ad-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="dd6ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dd6ad-105">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="dd6ad-106">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="dd6ad-107">Pliki ustawień, takie jak *:::no-loc(appsettings.json):::*</span><span class="sxs-lookup"><span data-stu-id="dd6ad-107">Settings files, such as *:::no-loc(appsettings.json):::*</span></span>
* <span data-ttu-id="dd6ad-108">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-108">Environment variables</span></span>
* <span data-ttu-id="dd6ad-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dd6ad-109">Azure Key Vault</span></span>
* <span data-ttu-id="dd6ad-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="dd6ad-110">Azure App Configuration</span></span>
* <span data-ttu-id="dd6ad-111">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-111">Command-line arguments</span></span>
* <span data-ttu-id="dd6ad-112">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="dd6ad-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="dd6ad-113">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="dd6ad-113">Directory files</span></span>
* <span data-ttu-id="dd6ad-114">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="dd6ad-114">In-memory .NET objects</span></span>

<span data-ttu-id="dd6ad-115">Ten temat zawiera informacje dotyczące konfiguracji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="dd6ad-116">Aby uzyskać informacje na temat korzystania z konfiguracji w aplikacjach konsolowych, zobacz [Konfiguracja platformy .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="dd6ad-117">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd6ad-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="dd6ad-118">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="dd6ad-118">Default configuration</span></span>

<span data-ttu-id="dd6ad-119">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="dd6ad-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="dd6ad-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący element `IConfiguration` jako źródło.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="dd6ad-122">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="dd6ad-123">[:::no-loc(appsettings.json):::](#appsettingsjson) Korzystanie z [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-123">[:::no-loc(appsettings.json):::](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="dd6ad-124">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="dd6ad-125">Na przykład *AppSettings* . ***Produkcja \* \* _._json* i *AppSettings* . \* \* \* programowanie** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-125">For example, *appsettings* . ***Production\*\*_._json* and *appsettings* .\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="dd6ad-126">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="dd6ad-127">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="dd6ad-128">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="dd6ad-129">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="dd6ad-130">Na przykład jeśli `MyKey` jest ustawiona w obu *:::no-loc(appsettings.json):::* i środowisku, wartość środowiska jest używana.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-130">For example, if `MyKey` is set in both *:::no-loc(appsettings.json):::* and the environment, the environment value is used.</span></span> <span data-ttu-id="dd6ad-131">Przy użyciu domyślnych dostawców konfiguracji  [dostawca konfiguracji wiersza polecenia](#clcp) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="dd6ad-132">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="dd6ad-133">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### :::no-loc(appsettings.json):::

<span data-ttu-id="dd6ad-134">Weź pod uwagę następujący *:::no-loc(appsettings.json):::* plik:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-134">Consider the following *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="dd6ad-135">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-136">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="dd6ad-137">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings* . ***Produkcja \* \* _._json* i *AppSettings* . \* \* \* pliki deweloperskie** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-137">*appsettings.*`Environment`*.json* : For example, the *appsettings* . ***Production\*\*_._json* and *appsettings* .\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="dd6ad-138">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="dd6ad-139">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="dd6ad-140">*AppSettings* . `Environment` . wartości *JSON* przesłaniają klucze w *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-140">*appsettings* .`Environment`. *json* values override keys in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="dd6ad-141">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-141">For example, by default:</span></span>

* <span data-ttu-id="dd6ad-142">W programowaniu, *AppSettings* . \* **Development** _._json \* konfiguracja zastępuje wartości Znalezione w *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-142">In development, *appsettings* .\* **Development** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::* .</span></span>
* <span data-ttu-id="dd6ad-143">W środowisku produkcyjnym *AppSettings* . \* **Production** _._json \* konfiguracja zastępuje wartości Znalezione w *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-143">In production, *appsettings* .\* **Production** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="dd6ad-144">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="dd6ad-145">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="dd6ad-146">Przy użyciu konfiguracji [domyślnej](#default) , *:::no-loc(appsettings.json):::* a *appSettings.* `Environment` pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-146">Using the [default](#default) configuration, the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="dd6ad-147">Zmiany wprowadzone w *:::no-loc(appsettings.json):::* i *appSettings.* `Environment` plik *. JSON* \* **po** _ uruchomienia aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-147">Changes made to the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* file \* **after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="dd6ad-148">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="dd6ad-149">Łączenie kolekcji usług</span><span class="sxs-lookup"><span data-stu-id="dd6ad-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="dd6ad-150">Security and Secret Manager</span><span class="sxs-lookup"><span data-stu-id="dd6ad-150">Security and secret manager</span></span>

<span data-ttu-id="dd6ad-151">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-151">Configuration data guidelines:</span></span>

<span data-ttu-id="dd6ad-152">Nigdy nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="dd6ad-153">Za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="dd6ad-154">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="dd6ad-155">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="dd6ad-156">[Domyślnie](#default)program [Secret Manager](xref:security/app-secrets) odczytuje ustawienia konfiguracji po *:::no-loc(appsettings.json):::* i *appSettings.* `Environment` *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* .</span></span>

<span data-ttu-id="dd6ad-157">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="dd6ad-158"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="dd6ad-159">Menedżer wpisów tajnych używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="dd6ad-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="dd6ad-161">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="dd6ad-162">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-162">Environment variables</span></span>

<span data-ttu-id="dd6ad-163">Przy użyciu konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> ładowana konfiguracja ze zmiennej środowiskowej par klucz-wartość po odczytu *:::no-loc(appsettings.json):::* , *appSettings.* `Environment` *. JSON* i [Menedżer wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="dd6ad-164">W związku z tym kluczowe wartości są odczytywane z wartości zastąpienia środowiska odczytywane z *:::no-loc(appsettings.json):::* , *appSettings.* `Environment` *. JSON* i Menedżer wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-164">Therefore, key values read from the environment override values read from *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="dd6ad-165">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-165">The following `set` commands:</span></span>

* <span data-ttu-id="dd6ad-166">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="dd6ad-167">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="dd6ad-168">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="dd6ad-169">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-169">The preceding environment settings:</span></span>

* <span data-ttu-id="dd6ad-170">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="dd6ad-171">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="dd6ad-172">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="dd6ad-173">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="dd6ad-174">`/M` ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="dd6ad-175">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="dd6ad-176">Aby sprawdzić, czy poprzednie polecenia zastępują *:::no-loc(appsettings.json):::* i *appSettings.* `Environment` *. JSON* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-176">To test that the preceding commands override *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* :</span></span>

* <span data-ttu-id="dd6ad-177">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="dd6ad-178">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i wprowadź `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="dd6ad-179">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="dd6ad-180">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-180">In the preceding code:</span></span>

* <span data-ttu-id="dd6ad-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="dd6ad-182">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="dd6ad-183">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="dd6ad-184">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="dd6ad-185">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="dd6ad-186">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="dd6ad-187">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="dd6ad-188">`DOTNET_` `ASPNETCORE_` Prefiksy i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="dd6ad-189">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="dd6ad-190">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="dd6ad-191">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="dd6ad-192">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="dd6ad-193">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-193">Exposed as environment variables.</span></span>

<span data-ttu-id="dd6ad-194">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="dd6ad-195">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="dd6ad-196">Zmienne środowiskowe ustawione w launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="dd6ad-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="dd6ad-197">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="dd6ad-198">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-198">Command-line</span></span>

<span data-ttu-id="dd6ad-199">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="dd6ad-200">*:::no-loc(appsettings.json):::* i *AppSettings* . `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-200">*:::no-loc(appsettings.json):::* and *appsettings* .`Environment`. *json* files.</span></span>
* <span data-ttu-id="dd6ad-201">Wpisy [tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dd6ad-202">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-202">Environment variables.</span></span>

<span data-ttu-id="dd6ad-203">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="dd6ad-204">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-204">Command-line arguments</span></span>

<span data-ttu-id="dd6ad-205">Następujące polecenie ustawia klucze i wartości przy użyciu `=` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="dd6ad-206">Następujące polecenie ustawia klucze i wartości przy użyciu `/` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="dd6ad-207">Następujące polecenie ustawia klucze i wartości przy użyciu `--` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="dd6ad-208">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-208">The key value:</span></span>

* <span data-ttu-id="dd6ad-209">Musi `=` być zgodna lub musi mieć prefiks lub, gdy wartość znajduje się w `--` `/` miejscu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="dd6ad-210">Nie jest wymagane, jeśli `=` jest używany.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="dd6ad-211">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="dd6ad-212">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które są używane `=` z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="dd6ad-213">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="dd6ad-213">Switch mappings</span></span>

<span data-ttu-id="dd6ad-214">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="dd6ad-215">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="dd6ad-216">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="dd6ad-217">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="dd6ad-218">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="dd6ad-219">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="dd6ad-220">Przełączniki muszą zaczynać się od `-` lub `--` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="dd6ad-221">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="dd6ad-222">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="dd6ad-223">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-224">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="dd6ad-225">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="dd6ad-226">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dd6ad-227">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="dd6ad-228">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-228">Hierarchical configuration data</span></span>

<span data-ttu-id="dd6ad-229">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="dd6ad-230">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący *:::no-loc(appsettings.json):::* plik:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="dd6ad-231">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-232">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="dd6ad-233">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="dd6ad-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="dd6ad-235">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="dd6ad-236">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-236">Configuration keys and values</span></span>

<span data-ttu-id="dd6ad-237">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-237">Configuration keys:</span></span>

* <span data-ttu-id="dd6ad-238">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-238">Are case-insensitive.</span></span> <span data-ttu-id="dd6ad-239">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="dd6ad-240">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="dd6ad-241">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="dd6ad-242">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="dd6ad-242">Hierarchical keys</span></span>
  * <span data-ttu-id="dd6ad-243">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="dd6ad-244">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="dd6ad-245">Podwójne podkreślenie, `__` ,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="dd6ad-246">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="dd6ad-247">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="dd6ad-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dd6ad-249">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="dd6ad-250">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-250">Configuration values:</span></span>

* <span data-ttu-id="dd6ad-251">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-251">Are strings.</span></span>
* <span data-ttu-id="dd6ad-252">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="dd6ad-253">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-253">Configuration providers</span></span>

<span data-ttu-id="dd6ad-254">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="dd6ad-255">Dostawca</span><span class="sxs-lookup"><span data-stu-id="dd6ad-255">Provider</span></span> | <span data-ttu-id="dd6ad-256">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="dd6ad-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="dd6ad-257">Dostawca konfiguracji Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dd6ad-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="dd6ad-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dd6ad-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="dd6ad-259">Dostawca konfiguracji aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="dd6ad-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="dd6ad-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="dd6ad-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="dd6ad-261">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="dd6ad-262">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-262">Command-line parameters</span></span> |
| [<span data-ttu-id="dd6ad-263">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="dd6ad-264">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-264">Custom source</span></span> |
| [<span data-ttu-id="dd6ad-265">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="dd6ad-266">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-266">Environment variables</span></span> |
| [<span data-ttu-id="dd6ad-267">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="dd6ad-268">Pliki INI, JSON i XML</span><span class="sxs-lookup"><span data-stu-id="dd6ad-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="dd6ad-269">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="dd6ad-270">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="dd6ad-270">Directory files</span></span> |
| [<span data-ttu-id="dd6ad-271">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="dd6ad-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="dd6ad-272">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="dd6ad-272">In-memory collections</span></span> |
| [<span data-ttu-id="dd6ad-273">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="dd6ad-274">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="dd6ad-274">File in the user profile directory</span></span> |

<span data-ttu-id="dd6ad-275">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="dd6ad-276">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="dd6ad-277">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-277">A typical sequence of configuration providers is:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="dd6ad-278">*AppSettings* . `Environment` . *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="dd6ad-278">*appsettings* .`Environment`. *json*</span></span>
1. [<span data-ttu-id="dd6ad-279">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="dd6ad-280">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="dd6ad-281">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="dd6ad-282">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="dd6ad-283">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="dd6ad-284">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-284">Connection string prefixes</span></span>

<span data-ttu-id="dd6ad-285">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="dd6ad-286">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="dd6ad-287">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="dd6ad-288">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-288">Connection string prefix</span></span> | <span data-ttu-id="dd6ad-289">Dostawca</span><span class="sxs-lookup"><span data-stu-id="dd6ad-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="dd6ad-290">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="dd6ad-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="dd6ad-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="dd6ad-292">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="dd6ad-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="dd6ad-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="dd6ad-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="dd6ad-294">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="dd6ad-295">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="dd6ad-296">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="dd6ad-297">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="dd6ad-297">Environment variable key</span></span> | <span data-ttu-id="dd6ad-298">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-298">Converted configuration key</span></span> | <span data-ttu-id="dd6ad-299">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-300">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-301">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dd6ad-302">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-303">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dd6ad-304">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-305">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dd6ad-306">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="dd6ad-307">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-307">File configuration provider</span></span>

<span data-ttu-id="dd6ad-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="dd6ad-309">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="dd6ad-310">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="dd6ad-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="dd6ad-311">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="dd6ad-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="dd6ad-312">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="dd6ad-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="dd6ad-313">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="dd6ad-313">INI configuration provider</span></span>

<span data-ttu-id="dd6ad-314"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="dd6ad-315">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="dd6ad-316">W powyższym kodzie ustawienia w *MyIniConfig.ini* i  *MyIniConfig* . `Environment` . pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig* .`Environment`. *ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="dd6ad-317">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="dd6ad-318">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dd6ad-319">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="dd6ad-320">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="dd6ad-321">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="dd6ad-321">JSON configuration provider</span></span>

<span data-ttu-id="dd6ad-322"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="dd6ad-323">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-323">Overloads can specify:</span></span>

* <span data-ttu-id="dd6ad-324">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-324">Whether the file is optional.</span></span>
* <span data-ttu-id="dd6ad-325">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="dd6ad-326">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="dd6ad-327">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-327">The preceding code:</span></span>

* <span data-ttu-id="dd6ad-328">Konfiguruje dostawcę konfiguracji JSON w celu załadowania *MyConfig.jsw* pliku z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="dd6ad-329">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="dd6ad-330">`reloadOnChange: true` : Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="dd6ad-331">Odczytuje [domyślnych dostawców konfiguracji](#default) przed *MyConfig.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="dd6ad-332">Ustawienia w *MyConfig.js* ustawienia przesłania pliku w domyślnych dostawcach konfiguracji, w tym [dostawca konfiguracji zmiennych środowiskowych](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dd6ad-333">Zazwyczaj \* **nie** należy przeznaczać wartości niestandardowych plików JSON, które są zastępowane przez [dostawcę konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-333">You typically \* **don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dd6ad-334">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="dd6ad-335">W powyższym kodzie ustawienia w _MyConfig.jsna \* i dla  *konfiguracji* . `Environment` .. pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-335">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig* .`Environment`. *json* files:</span></span>

* <span data-ttu-id="dd6ad-336">Zastąp ustawienia w *:::no-loc(appsettings.json):::* i *AppSettings* . `Environment` .. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-336">Override settings in the *:::no-loc(appsettings.json):::* and *appsettings* .`Environment`. *json* files.</span></span>
* <span data-ttu-id="dd6ad-337">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dd6ad-338">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujące *MyConfig.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="dd6ad-339">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="dd6ad-340">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="dd6ad-340">XML configuration provider</span></span>

<span data-ttu-id="dd6ad-341"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="dd6ad-342">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="dd6ad-343">W powyższym kodzie ustawienia w *MyXMLFile.xml* i  *MyXMLFile* . `Environment` . pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile* .`Environment`. *xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="dd6ad-344">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="dd6ad-345">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="dd6ad-346">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="dd6ad-347">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-348">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="dd6ad-349">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-350">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="dd6ad-351">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dd6ad-352">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="dd6ad-352">key:attribute</span></span>
* <span data-ttu-id="dd6ad-353">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="dd6ad-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="dd6ad-354">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="dd6ad-355"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="dd6ad-356">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-356">The key is the file name.</span></span> <span data-ttu-id="dd6ad-357">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-357">The value contains the file's contents.</span></span> <span data-ttu-id="dd6ad-358">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="dd6ad-359">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="dd6ad-360">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="dd6ad-361">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="dd6ad-362">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="dd6ad-363">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="dd6ad-364">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="dd6ad-365">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="dd6ad-366">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="dd6ad-367">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="dd6ad-367">Memory configuration provider</span></span>

<span data-ttu-id="dd6ad-368"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="dd6ad-369">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="dd6ad-370">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-371">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="dd6ad-372">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="dd6ad-373">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="dd6ad-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="dd6ad-374">GetValue</span></span>

<span data-ttu-id="dd6ad-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-376">W powyższym kodzie, jeśli `NumberKey` nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="dd6ad-377">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="dd6ad-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="dd6ad-378">W poniższych przykładach należy wziąć pod uwagę następujące *MySubsection.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="dd6ad-379">Poniższy kod dodaje *MySubsection.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="dd6ad-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="dd6ad-380">GetSection</span></span>

<span data-ttu-id="dd6ad-381">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="dd6ad-382">Poniższy kod zwraca wartości dla `section1` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-383">Poniższy kod zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-384">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="dd6ad-385">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="dd6ad-386">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="dd6ad-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="dd6ad-388">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="dd6ad-388">GetChildren and Exists</span></span>

<span data-ttu-id="dd6ad-389">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-390">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="dd6ad-391">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="dd6ad-391">Bind an array</span></span>

<span data-ttu-id="dd6ad-392">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dd6ad-393">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="dd6ad-394">Rozważ *MyArray.jsna* podstawie pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="dd6ad-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="dd6ad-395">Poniższy kod dodaje *MyArray.js* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="dd6ad-396">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-397">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="dd6ad-398">W poprzednich danych wyjściowych indeks 3 ma wartość `value40` odpowiadającą wartości `"4": "value40",` w *MyArray.jsna* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json* .</span></span> <span data-ttu-id="dd6ad-399">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="dd6ad-400">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="dd6ad-401">Poniższy kod ładuje `array:entries` konfigurację z użyciem <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="dd6ad-402">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-403">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="dd6ad-404">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="dd6ad-405">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="dd6ad-406">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="dd6ad-407">Brakujący element konfiguracji dla indeksu &num; 3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje &num; parę klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="dd6ad-408">Rozważmy następujące *Value3.js* pliku z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="dd6ad-409">Poniższy kod zawiera konfigurację dla *Value3.js* i `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="dd6ad-410">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-411">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="dd6ad-412">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="dd6ad-413">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-413">Custom configuration provider</span></span>

<span data-ttu-id="dd6ad-414">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="dd6ad-415">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="dd6ad-416">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="dd6ad-417">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="dd6ad-418">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="dd6ad-419">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="dd6ad-420">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="dd6ad-421">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="dd6ad-422">*Modele/EFConfigurationValue. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-422">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="dd6ad-423">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="dd6ad-424">*EFConfigurationProvider/EFConfigurationContext. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="dd6ad-425">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="dd6ad-426">*EFConfigurationProvider/EFConfigurationSource. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="dd6ad-427">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="dd6ad-428">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="dd6ad-429">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="dd6ad-430">*EFConfigurationProvider/EFConfigurationProvider. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="dd6ad-431">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="dd6ad-432">*Rozszerzenia/EntityFrameworkExtensions. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-432">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="dd6ad-433">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="dd6ad-434">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="dd6ad-434">Access configuration in Startup</span></span>

<span data-ttu-id="dd6ad-435">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="dd6ad-436">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="dd6ad-437">Konfiguracja dostępu na :::no-loc(Razor)::: stronach</span><span class="sxs-lookup"><span data-stu-id="dd6ad-437">Access configuration in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="dd6ad-438">Poniższy kod przedstawia dane konfiguracji na :::no-loc(Razor)::: stronie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-438">The following code displays configuration data in a :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="dd6ad-439">W poniższym kodzie `MyOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="dd6ad-440">Następujące oznakowanie używa [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: dyrektywy do rozwiązywania i wyświetlania wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="dd6ad-441">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="dd6ad-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="dd6ad-442">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="dd6ad-443">Konfigurowanie opcji za pomocą delegata</span><span class="sxs-lookup"><span data-stu-id="dd6ad-443">Configure options with a delegate</span></span>

<span data-ttu-id="dd6ad-444">Opcje skonfigurowane w wartościach zastąpień delegatów ustawionych w dostawcach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="dd6ad-445">Konfigurowanie opcji za pomocą delegata jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="dd6ad-446">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="dd6ad-447">Używa delegata do konfigurowania wartości dla `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="dd6ad-448">Poniższy kod wyświetla wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="dd6ad-449">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w, *:::no-loc(appsettings.json):::* a następnie zastąpione przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-449">In the preceding example, the values of `Option1` and `Option2` are specified in *:::no-loc(appsettings.json):::* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="dd6ad-450">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-450">Host versus app configuration</span></span>

<span data-ttu-id="dd6ad-451">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="dd6ad-452">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="dd6ad-453">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="dd6ad-454">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="dd6ad-455">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="dd6ad-456">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="dd6ad-456">Default host configuration</span></span>

<span data-ttu-id="dd6ad-457">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="dd6ad-458">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="dd6ad-459">Zmienne środowiskowe poprzedzone znakiem `DOTNET_` (na przykład `DOTNET_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="dd6ad-460">Prefiks ( `DOTNET_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="dd6ad-461">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="dd6ad-462">Konfiguracja domyślna hosta sieci Web ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="dd6ad-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="dd6ad-463">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="dd6ad-464">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="dd6ad-465">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` zmienna środowiskowa jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="dd6ad-466">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="dd6ad-467">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="dd6ad-467">Other configuration</span></span>

<span data-ttu-id="dd6ad-468">Ten temat dotyczy tylko *konfiguracji aplikacji* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-468">This topic only pertains to *app configuration* .</span></span> <span data-ttu-id="dd6ad-469">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="dd6ad-470">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="dd6ad-471">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="dd6ad-472">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="dd6ad-473">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="dd6ad-474">Zmienne środowiskowe ustawione w *launchSettings.jsna* zastępują te ustawienia w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="dd6ad-475">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="dd6ad-476">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="dd6ad-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="dd6ad-477"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="dd6ad-478">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd6ad-479">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="dd6ad-479">Additional resources</span></span>

* [<span data-ttu-id="dd6ad-480">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dd6ad-481">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers* .</span></span> <span data-ttu-id="dd6ad-482">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="dd6ad-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dd6ad-483">Azure Key Vault</span></span>
* <span data-ttu-id="dd6ad-484">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="dd6ad-484">Azure App Configuration</span></span>
* <span data-ttu-id="dd6ad-485">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-485">Command-line arguments</span></span>
* <span data-ttu-id="dd6ad-486">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="dd6ad-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="dd6ad-487">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="dd6ad-487">Directory files</span></span>
* <span data-ttu-id="dd6ad-488">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-488">Environment variables</span></span>
* <span data-ttu-id="dd6ad-489">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="dd6ad-489">In-memory .NET objects</span></span>
* <span data-ttu-id="dd6ad-490">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="dd6ad-490">Settings files</span></span>

<span data-ttu-id="dd6ad-491">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="dd6ad-492">Przykłady kodu, które obserwują i w przykładowej aplikacji używają <xref:Microsoft.Extensions.Configuration> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="dd6ad-493">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="dd6ad-494">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="dd6ad-495">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="dd6ad-496">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd6ad-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="dd6ad-497">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-497">Host versus app configuration</span></span>

<span data-ttu-id="dd6ad-498">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="dd6ad-499">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="dd6ad-500">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="dd6ad-501">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="dd6ad-502">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="dd6ad-503">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="dd6ad-503">Other configuration</span></span>

<span data-ttu-id="dd6ad-504">Ten temat dotyczy tylko *konfiguracji aplikacji* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-504">This topic only pertains to *app configuration* .</span></span> <span data-ttu-id="dd6ad-505">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="dd6ad-506">*launch.jsna* / *launchSettings.json* są plikami konfiguracyjnymi narzędzi dla środowiska programistycznego, opisanymi w temacie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="dd6ad-507">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="dd6ad-508">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="dd6ad-509">*web.config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="dd6ad-510">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="dd6ad-511">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="dd6ad-511">Default configuration</span></span>

<span data-ttu-id="dd6ad-512">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych szablonów dotnet](/dotnet/core/tools/dotnet-new) są wywoływane <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="dd6ad-513">`CreateDefaultBuilder` zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="dd6ad-514">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="dd6ad-515">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="dd6ad-516">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="dd6ad-517">Zmienne środowiskowe poprzedzone znakiem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="dd6ad-518">Prefiks ( `ASPNETCORE_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="dd6ad-519">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="dd6ad-520">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="dd6ad-521">*:::no-loc(appsettings.json):::* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-521">*:::no-loc(appsettings.json):::* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="dd6ad-522">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="dd6ad-523">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="dd6ad-524">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="dd6ad-525">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="dd6ad-526">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-526">Security</span></span>

<span data-ttu-id="dd6ad-527">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="dd6ad-528">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="dd6ad-529">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="dd6ad-530">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="dd6ad-531">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="dd6ad-532"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="dd6ad-533">Menedżer wpisów tajnych używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="dd6ad-534">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="dd6ad-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="dd6ad-536">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="dd6ad-537">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-537">Hierarchical configuration data</span></span>

<span data-ttu-id="dd6ad-538">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="dd6ad-539">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="dd6ad-540">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="dd6ad-541">Sekcje i klucze są spłaszczone przy użyciu dwukropka (), `:` Aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="dd6ad-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-542">section0:key0</span></span>
* <span data-ttu-id="dd6ad-543">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-543">section0:key1</span></span>
* <span data-ttu-id="dd6ad-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-544">section1:key0</span></span>
* <span data-ttu-id="dd6ad-545">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-545">section1:key1</span></span>

<span data-ttu-id="dd6ad-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="dd6ad-547">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="dd6ad-548">Konwencje</span><span class="sxs-lookup"><span data-stu-id="dd6ad-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="dd6ad-549">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-549">Sources and providers</span></span>

<span data-ttu-id="dd6ad-550">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="dd6ad-551">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="dd6ad-552">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="dd6ad-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dd6ad-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> można wstrzyknąć do :::no-loc(Razor)::: stron <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> lub MVC, <xref:Microsoft.AspNetCore.Mvc.Controller> Aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="dd6ad-555">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="dd6ad-556">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="dd6ad-557">Klucze</span><span class="sxs-lookup"><span data-stu-id="dd6ad-557">Keys</span></span>

<span data-ttu-id="dd6ad-558">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="dd6ad-559">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-559">Keys are case-insensitive.</span></span> <span data-ttu-id="dd6ad-560">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="dd6ad-561">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="dd6ad-562">Aby uzyskać więcej informacji na temat zduplikowanych kluczy JSON, zobacz [ten problem](https://github.com/dotnet/extensions/issues/2381)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="dd6ad-563">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="dd6ad-563">Hierarchical keys</span></span>
  * <span data-ttu-id="dd6ad-564">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="dd6ad-565">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="dd6ad-566">Podwójne podkreślenie ( `__` ) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="dd6ad-567">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="dd6ad-568">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="dd6ad-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dd6ad-570">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="dd6ad-571">Wartości</span><span class="sxs-lookup"><span data-stu-id="dd6ad-571">Values</span></span>

<span data-ttu-id="dd6ad-572">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="dd6ad-573">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-573">Values are strings.</span></span>
* <span data-ttu-id="dd6ad-574">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="dd6ad-575">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-575">Providers</span></span>

<span data-ttu-id="dd6ad-576">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="dd6ad-577">Dostawca</span><span class="sxs-lookup"><span data-stu-id="dd6ad-577">Provider</span></span> | <span data-ttu-id="dd6ad-578">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="dd6ad-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="dd6ad-579">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące *zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="dd6ad-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) ( *Security* topics)</span></span> | <span data-ttu-id="dd6ad-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dd6ad-580">Azure Key Vault</span></span> |
| <span data-ttu-id="dd6ad-581">[Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="dd6ad-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="dd6ad-582">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="dd6ad-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="dd6ad-583">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="dd6ad-584">Parametry wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-584">Command-line parameters</span></span> |
| [<span data-ttu-id="dd6ad-585">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="dd6ad-586">Źródło niestandardowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-586">Custom source</span></span> |
| [<span data-ttu-id="dd6ad-587">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="dd6ad-588">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-588">Environment variables</span></span> |
| [<span data-ttu-id="dd6ad-589">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="dd6ad-590">Pliki (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="dd6ad-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="dd6ad-591">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="dd6ad-592">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="dd6ad-592">Directory files</span></span> |
| [<span data-ttu-id="dd6ad-593">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="dd6ad-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="dd6ad-594">Kolekcje w pamięci</span><span class="sxs-lookup"><span data-stu-id="dd6ad-594">In-memory collections</span></span> |
| <span data-ttu-id="dd6ad-595">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tematy dotyczące *zabezpieczeń* )</span><span class="sxs-lookup"><span data-stu-id="dd6ad-595">[User secrets (Secret Manager)](xref:security/app-secrets) ( *Security* topics)</span></span> | <span data-ttu-id="dd6ad-596">Plik w katalogu profilu użytkownika</span><span class="sxs-lookup"><span data-stu-id="dd6ad-596">File in the user profile directory</span></span> |

<span data-ttu-id="dd6ad-597">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="dd6ad-598">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="dd6ad-599">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="dd6ad-600">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="dd6ad-601">Pliki ( *:::no-loc(appsettings.json):::* , *appSettings. { Environment}. JSON* , gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="dd6ad-601">Files ( *:::no-loc(appsettings.json):::* , *appsettings.{Environment}.json* , where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="dd6ad-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="dd6ad-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="dd6ad-603">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="dd6ad-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="dd6ad-604">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dd6ad-604">Environment variables</span></span>
1. <span data-ttu-id="dd6ad-605">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-605">Command-line arguments</span></span>

<span data-ttu-id="dd6ad-606">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="dd6ad-607">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu programu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dd6ad-608">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="dd6ad-609">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="dd6ad-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="dd6ad-610">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="dd6ad-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="dd6ad-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="dd6ad-612">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="dd6ad-613">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="dd6ad-614">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="dd6ad-615">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="dd6ad-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="dd6ad-616">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder` , najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="dd6ad-617">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-617">Consume configuration during app startup</span></span>

<span data-ttu-id="dd6ad-618">Konfiguracja dostarczona do aplikacji w programie `ConfigureAppConfiguration` jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dd6ad-619">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="dd6ad-620">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="dd6ad-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="dd6ad-622">Aby uaktywnić konfigurację wiersza polecenia, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dd6ad-623">`AddCommandLine` jest wywoływana automatycznie, gdy `CreateDefaultBuilder(string [])` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="dd6ad-624">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dd6ad-625">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="dd6ad-626">Opcjonalna konfiguracja z *:::no-loc(appsettings.json):::* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-626">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="dd6ad-627">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dd6ad-628">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-628">Environment variables.</span></span>

<span data-ttu-id="dd6ad-629">`CreateDefaultBuilder` dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="dd6ad-630">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="dd6ad-631">`CreateDefaultBuilder` działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="dd6ad-632">W związku z tym konfiguracja wiersza polecenia aktywowana przez program `CreateDefaultBuilder` może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="dd6ad-633">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dd6ad-634">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj jako `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="dd6ad-635">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-635">**Example**</span></span>

<span data-ttu-id="dd6ad-636">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="dd6ad-637">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="dd6ad-638">Podaj do polecenia argument wiersza polecenia `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="dd6ad-639">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="dd6ad-640">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="dd6ad-641">Argumenty</span><span class="sxs-lookup"><span data-stu-id="dd6ad-641">Arguments</span></span>

<span data-ttu-id="dd6ad-642">Wartość musi następować po znaku równości ( `=` ) lub klucz musi mieć prefiks ( `--` lub `/` ), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="dd6ad-643">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="dd6ad-644">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="dd6ad-644">Key prefix</span></span>               | <span data-ttu-id="dd6ad-645">Przykład</span><span class="sxs-lookup"><span data-stu-id="dd6ad-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="dd6ad-646">Brak prefiksu</span><span class="sxs-lookup"><span data-stu-id="dd6ad-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="dd6ad-647">Dwie kreski ( `--` )</span><span class="sxs-lookup"><span data-stu-id="dd6ad-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="dd6ad-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="dd6ad-649">Ukośnik ( `/` )</span><span class="sxs-lookup"><span data-stu-id="dd6ad-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="dd6ad-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="dd6ad-651">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="dd6ad-652">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="dd6ad-653">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="dd6ad-653">Switch mappings</span></span>

<span data-ttu-id="dd6ad-654">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="dd6ad-655">Podczas ręcznego kompilowania konfiguracji za pomocą programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="dd6ad-656">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="dd6ad-657">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="dd6ad-658">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="dd6ad-659">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="dd6ad-660">Przełączniki muszą zaczynać się kreską ( `-` ) lub podwójną kreską ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="dd6ad-661">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="dd6ad-662">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="dd6ad-663">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="dd6ad-664">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="dd6ad-665">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="dd6ad-666">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dd6ad-667">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="dd6ad-668">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="dd6ad-669">Klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-669">Key</span></span>       | <span data-ttu-id="dd6ad-670">Wartość</span><span class="sxs-lookup"><span data-stu-id="dd6ad-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="dd6ad-671">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="dd6ad-672">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="dd6ad-673">Klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-673">Key</span></span>               | <span data-ttu-id="dd6ad-674">Wartość</span><span class="sxs-lookup"><span data-stu-id="dd6ad-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="dd6ad-675">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="dd6ad-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="dd6ad-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="dd6ad-677">Aby uaktywnić konfigurację zmiennych środowiskowych, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="dd6ad-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="dd6ad-679">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="dd6ad-680">`AddEnvironmentVariables` służy do ładowania zmiennych środowiskowych, które są poprzedzone `ASPNETCORE_` [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="dd6ad-681">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dd6ad-682">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="dd6ad-683">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez wywołanie `AddEnvironmentVariables` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="dd6ad-684">Opcjonalna konfiguracja z *:::no-loc(appsettings.json):::* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-684">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="dd6ad-685">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dd6ad-686">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-686">Command-line arguments.</span></span>

<span data-ttu-id="dd6ad-687">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="dd6ad-688">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="dd6ad-689">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddEnvironmentVariables` z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="dd6ad-690">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="dd6ad-691">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-691">**Example**</span></span>

<span data-ttu-id="dd6ad-692">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="dd6ad-693">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-693">Run the sample app.</span></span> <span data-ttu-id="dd6ad-694">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="dd6ad-695">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej środowiskowej `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="dd6ad-696">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj w `Development` przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="dd6ad-697">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="dd6ad-698">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="dd6ad-699">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić `FilteredConfiguration` stronę na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="dd6ad-700">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-700">Prefixes</span></span>

<span data-ttu-id="dd6ad-701">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="dd6ad-702">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_` , podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="dd6ad-703">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="dd6ad-704">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="dd6ad-705">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dd6ad-706">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-706">**Connection string prefixes**</span></span>

<span data-ttu-id="dd6ad-707">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="dd6ad-708">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="dd6ad-709">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="dd6ad-709">Connection string prefix</span></span> | <span data-ttu-id="dd6ad-710">Dostawca</span><span class="sxs-lookup"><span data-stu-id="dd6ad-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="dd6ad-711">Dostawca niestandardowy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="dd6ad-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="dd6ad-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="dd6ad-713">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="dd6ad-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="dd6ad-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="dd6ad-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="dd6ad-715">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="dd6ad-716">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="dd6ad-717">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="dd6ad-718">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="dd6ad-718">Environment variable key</span></span> | <span data-ttu-id="dd6ad-719">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-719">Converted configuration key</span></span> | <span data-ttu-id="dd6ad-720">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="dd6ad-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-721">Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-722">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dd6ad-723">Wartość: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-724">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dd6ad-725">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="dd6ad-726">Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="dd6ad-727">Wartość: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="dd6ad-728">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-728">**Example**</span></span>

<span data-ttu-id="dd6ad-729">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="dd6ad-730">Nazwa: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="dd6ad-731">Wartość: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="dd6ad-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="dd6ad-732">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config` , odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="dd6ad-733">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-733">File Configuration Provider</span></span>

<span data-ttu-id="dd6ad-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="dd6ad-735">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="dd6ad-736">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="dd6ad-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="dd6ad-737">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="dd6ad-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="dd6ad-738">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="dd6ad-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="dd6ad-739">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="dd6ad-739">INI Configuration Provider</span></span>

<span data-ttu-id="dd6ad-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="dd6ad-741">Aby uaktywnić konfigurację pliku INI, wywołaj <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dd6ad-742">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="dd6ad-743">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="dd6ad-744">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-744">Whether the file is optional.</span></span>
* <span data-ttu-id="dd6ad-745">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="dd6ad-746"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="dd6ad-747">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="dd6ad-748">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="dd6ad-749">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dd6ad-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-750">section0:key0</span></span>
* <span data-ttu-id="dd6ad-751">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-751">section0:key1</span></span>
* <span data-ttu-id="dd6ad-752">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="dd6ad-752">section1:subsection:key</span></span>
* <span data-ttu-id="dd6ad-753">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-753">section2:subsection0:key</span></span>
* <span data-ttu-id="dd6ad-754">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="dd6ad-755">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="dd6ad-755">JSON Configuration Provider</span></span>

<span data-ttu-id="dd6ad-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="dd6ad-757">Aby uaktywnić konfigurację pliku JSON, wywołaj <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dd6ad-758">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="dd6ad-759">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-759">Whether the file is optional.</span></span>
* <span data-ttu-id="dd6ad-760">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="dd6ad-761"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="dd6ad-762">`AddJsonFile` jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dd6ad-763">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="dd6ad-764">*:::no-loc(appsettings.json):::* : Ten plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-764">*:::no-loc(appsettings.json):::* : This file is read first.</span></span> <span data-ttu-id="dd6ad-765">Wersja środowiska pliku może przesłonić wartości dostarczone przez *:::no-loc(appsettings.json):::* plik.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-765">The environment version of the file can override the values provided by the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="dd6ad-766">*appSettings. {Environment}. JSON* : wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-766">*appsettings.{Environment}.json* : The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="dd6ad-767">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="dd6ad-768">`CreateDefaultBuilder` ładuje również:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="dd6ad-769">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-769">Environment variables.</span></span>
* <span data-ttu-id="dd6ad-770">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="dd6ad-771">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-771">Command-line arguments.</span></span>

<span data-ttu-id="dd6ad-772">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="dd6ad-773">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="dd6ad-774">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *:::no-loc(appsettings.json):::* i *appSettings. { Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* :</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="dd6ad-775">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-775">**Example**</span></span>

<span data-ttu-id="dd6ad-776">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="dd6ad-777">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-777">The first call to `AddJsonFile` loads configuration from *:::no-loc(appsettings.json):::* :</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/:::no-loc(appsettings.json):::)]

* <span data-ttu-id="dd6ad-778">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json* .</span></span> <span data-ttu-id="dd6ad-779">W przypadku *appsettings.Development.js* w aplikacji przykładowej załadowano następujący plik:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="dd6ad-780">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-780">Run the sample app.</span></span> <span data-ttu-id="dd6ad-781">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="dd6ad-782">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="dd6ad-783">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="dd6ad-784">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="dd6ad-785">Otwórz *Właściwości/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="dd6ad-786">W `ConfigurationSample` profilu Zmień wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej na `Production` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="dd6ad-787">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="dd6ad-788">Ustawienia w *appsettings.Development.js* nie przesłaniają już ustawień w programie *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-788">The settings in the *appsettings.Development.json* no longer override the settings in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="dd6ad-789">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="dd6ad-790">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="dd6ad-790">XML Configuration Provider</span></span>

<span data-ttu-id="dd6ad-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="dd6ad-792">Aby uaktywnić konfigurację pliku XML, wywołaj <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dd6ad-793">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="dd6ad-794">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-794">Whether the file is optional.</span></span>
* <span data-ttu-id="dd6ad-795">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="dd6ad-796"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="dd6ad-797">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="dd6ad-798">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="dd6ad-799">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="dd6ad-800">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="dd6ad-801">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dd6ad-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-802">section0:key0</span></span>
* <span data-ttu-id="dd6ad-803">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-803">section0:key1</span></span>
* <span data-ttu-id="dd6ad-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-804">section1:key0</span></span>
* <span data-ttu-id="dd6ad-805">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-805">section1:key1</span></span>

<span data-ttu-id="dd6ad-806">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="dd6ad-807">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dd6ad-808">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-808">section:section0:key:key0</span></span>
* <span data-ttu-id="dd6ad-809">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-809">section:section0:key:key1</span></span>
* <span data-ttu-id="dd6ad-810">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-810">section:section1:key:key0</span></span>
* <span data-ttu-id="dd6ad-811">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-811">section:section1:key:key1</span></span>

<span data-ttu-id="dd6ad-812">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="dd6ad-813">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="dd6ad-814">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="dd6ad-814">key:attribute</span></span>
* <span data-ttu-id="dd6ad-815">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="dd6ad-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="dd6ad-816">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="dd6ad-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="dd6ad-817"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="dd6ad-818">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-818">The key is the file name.</span></span> <span data-ttu-id="dd6ad-819">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-819">The value contains the file's contents.</span></span> <span data-ttu-id="dd6ad-820">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="dd6ad-821">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="dd6ad-822">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="dd6ad-823">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="dd6ad-824">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="dd6ad-825">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="dd6ad-826">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="dd6ad-827">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="dd6ad-828">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="dd6ad-829">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="dd6ad-829">Memory Configuration Provider</span></span>

<span data-ttu-id="dd6ad-830"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="dd6ad-831">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="dd6ad-832">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="dd6ad-833">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="dd6ad-834">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="dd6ad-835">Słownik jest używany z wywołaniem do `AddInMemoryCollection` zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="dd6ad-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="dd6ad-836">GetValue</span></span>

<span data-ttu-id="dd6ad-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="dd6ad-838">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-838">An overload accepts a default value.</span></span>

<span data-ttu-id="dd6ad-839">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-839">The following example:</span></span>

* <span data-ttu-id="dd6ad-840">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="dd6ad-841">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="dd6ad-842">Typ wartości `int` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="dd6ad-843">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="dd6ad-844">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="dd6ad-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="dd6ad-845">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="dd6ad-846">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="dd6ad-847">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="dd6ad-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-848">section0:key0</span></span>
* <span data-ttu-id="dd6ad-849">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-849">section0:key1</span></span>
* <span data-ttu-id="dd6ad-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-850">section1:key0</span></span>
* <span data-ttu-id="dd6ad-851">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-851">section1:key1</span></span>
* <span data-ttu-id="dd6ad-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="dd6ad-853">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="dd6ad-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="dd6ad-855">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="dd6ad-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="dd6ad-856">GetSection</span></span>

<span data-ttu-id="dd6ad-857">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="dd6ad-858">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1` , wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="dd6ad-859">`configSection`Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="dd6ad-860">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0` , wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="dd6ad-861">`GetSection` nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="dd6ad-862">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="dd6ad-863">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="dd6ad-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="dd6ad-865">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="dd6ad-865">GetChildren</span></span>

<span data-ttu-id="dd6ad-866">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w programie `section2` uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="dd6ad-867">Exists</span><span class="sxs-lookup"><span data-stu-id="dd6ad-867">Exists</span></span>

<span data-ttu-id="dd6ad-868">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="dd6ad-869">Dane przykładowe są spowodowane tym, że `sectionExists` `false` `section2:subsection2` w danych konfiguracji nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="dd6ad-870">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="dd6ad-870">Bind to an object graph</span></span>

<span data-ttu-id="dd6ad-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="dd6ad-872">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="dd6ad-873">Przykład zawiera `TvShow` model, którego obiekt zawiera obiekty `Metadata` i `Actors` klasy ( *modele/TvShow. cs* ):</span><span class="sxs-lookup"><span data-stu-id="dd6ad-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes ( *Models/TvShow.cs* ):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="dd6ad-874">Przykładowa aplikacja zawiera plik *tvshow.xml* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="dd6ad-875">Konfiguracja jest powiązana z `TvShow` wykresem całego obiektu za pomocą `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="dd6ad-876">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="dd6ad-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="dd6ad-878">`Get<T>` jest wygodniejszy niż używanie `Bind` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="dd6ad-879">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="dd6ad-880">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="dd6ad-880">Bind an array to a class</span></span>

<span data-ttu-id="dd6ad-881">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="dd6ad-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="dd6ad-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="dd6ad-883">Każdy format tablicy, który ujawnia segment klucza numerycznego ( `:0:` , `:1:` , &hellip; `:{n}:` ) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="dd6ad-884">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-884">Binding is provided by convention.</span></span> <span data-ttu-id="dd6ad-885">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="dd6ad-886">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-886">**In-memory array processing**</span></span>

<span data-ttu-id="dd6ad-887">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="dd6ad-888">Klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-888">Key</span></span>             | <span data-ttu-id="dd6ad-889">Wartość</span><span class="sxs-lookup"><span data-stu-id="dd6ad-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="dd6ad-890">Tablica: wpisy: 0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-890">array:entries:0</span></span> | <span data-ttu-id="dd6ad-891">value0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-891">value0</span></span> |
| <span data-ttu-id="dd6ad-892">Tablica: wpisy: 1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-892">array:entries:1</span></span> | <span data-ttu-id="dd6ad-893">sekwencj</span><span class="sxs-lookup"><span data-stu-id="dd6ad-893">value1</span></span> |
| <span data-ttu-id="dd6ad-894">Tablica: wpisy: 2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-894">array:entries:2</span></span> | <span data-ttu-id="dd6ad-895">wartość2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-895">value2</span></span> |
| <span data-ttu-id="dd6ad-896">Tablica: wpisy: 4</span><span class="sxs-lookup"><span data-stu-id="dd6ad-896">array:entries:4</span></span> | <span data-ttu-id="dd6ad-897">value4</span><span class="sxs-lookup"><span data-stu-id="dd6ad-897">value4</span></span> |
| <span data-ttu-id="dd6ad-898">Tablica: wpisy: 5</span><span class="sxs-lookup"><span data-stu-id="dd6ad-898">array:entries:5</span></span> | <span data-ttu-id="dd6ad-899">value5</span><span class="sxs-lookup"><span data-stu-id="dd6ad-899">value5</span></span> |

<span data-ttu-id="dd6ad-900">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="dd6ad-901">Tablica pomija wartość dla indeksu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="dd6ad-902">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="dd6ad-903">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="dd6ad-904">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="dd6ad-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="dd6ad-906">Obiekt powiązany, wystąpienie elementu `ArrayExample` , otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="dd6ad-907">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="dd6ad-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="dd6ad-908">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="dd6ad-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="dd6ad-909">0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-909">0</span></span>                            | <span data-ttu-id="dd6ad-910">value0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-910">value0</span></span>                       |
| <span data-ttu-id="dd6ad-911">1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-911">1</span></span>                            | <span data-ttu-id="dd6ad-912">sekwencj</span><span class="sxs-lookup"><span data-stu-id="dd6ad-912">value1</span></span>                       |
| <span data-ttu-id="dd6ad-913">2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-913">2</span></span>                            | <span data-ttu-id="dd6ad-914">wartość2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-914">value2</span></span>                       |
| <span data-ttu-id="dd6ad-915">3</span><span class="sxs-lookup"><span data-stu-id="dd6ad-915">3</span></span>                            | <span data-ttu-id="dd6ad-916">value4</span><span class="sxs-lookup"><span data-stu-id="dd6ad-916">value4</span></span>                       |
| <span data-ttu-id="dd6ad-917">4</span><span class="sxs-lookup"><span data-stu-id="dd6ad-917">4</span></span>                            | <span data-ttu-id="dd6ad-918">value5</span><span class="sxs-lookup"><span data-stu-id="dd6ad-918">value5</span></span>                       |

<span data-ttu-id="dd6ad-919">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="dd6ad-920">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="dd6ad-921">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="dd6ad-922">Brakujący element konfiguracji dla indeksu &num; 3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="dd6ad-923">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="dd6ad-924">*missing_value.js* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-924">*missing_value.json* :</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="dd6ad-925">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="dd6ad-926">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="dd6ad-927">Klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-927">Key</span></span>             | <span data-ttu-id="dd6ad-928">Wartość</span><span class="sxs-lookup"><span data-stu-id="dd6ad-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="dd6ad-929">Tablica: wpisy: 3</span><span class="sxs-lookup"><span data-stu-id="dd6ad-929">array:entries:3</span></span> | <span data-ttu-id="dd6ad-930">Wartość3</span><span class="sxs-lookup"><span data-stu-id="dd6ad-930">value3</span></span> |

<span data-ttu-id="dd6ad-931">Jeśli `ArrayExample` wystąpienie klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num; 3, `ArrayExample.Entries` Tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="dd6ad-932">`ArrayExample.Entries` Indeks</span><span class="sxs-lookup"><span data-stu-id="dd6ad-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="dd6ad-933">`ArrayExample.Entries` Wartościami</span><span class="sxs-lookup"><span data-stu-id="dd6ad-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="dd6ad-934">0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-934">0</span></span>                            | <span data-ttu-id="dd6ad-935">value0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-935">value0</span></span>                       |
| <span data-ttu-id="dd6ad-936">1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-936">1</span></span>                            | <span data-ttu-id="dd6ad-937">sekwencj</span><span class="sxs-lookup"><span data-stu-id="dd6ad-937">value1</span></span>                       |
| <span data-ttu-id="dd6ad-938">2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-938">2</span></span>                            | <span data-ttu-id="dd6ad-939">wartość2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-939">value2</span></span>                       |
| <span data-ttu-id="dd6ad-940">3</span><span class="sxs-lookup"><span data-stu-id="dd6ad-940">3</span></span>                            | <span data-ttu-id="dd6ad-941">Wartość3</span><span class="sxs-lookup"><span data-stu-id="dd6ad-941">value3</span></span>                       |
| <span data-ttu-id="dd6ad-942">4</span><span class="sxs-lookup"><span data-stu-id="dd6ad-942">4</span></span>                            | <span data-ttu-id="dd6ad-943">value4</span><span class="sxs-lookup"><span data-stu-id="dd6ad-943">value4</span></span>                       |
| <span data-ttu-id="dd6ad-944">5</span><span class="sxs-lookup"><span data-stu-id="dd6ad-944">5</span></span>                            | <span data-ttu-id="dd6ad-945">value5</span><span class="sxs-lookup"><span data-stu-id="dd6ad-945">value5</span></span>                       |

<span data-ttu-id="dd6ad-946">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="dd6ad-946">**JSON array processing**</span></span>

<span data-ttu-id="dd6ad-947">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="dd6ad-948">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="dd6ad-949">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="dd6ad-950">Klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-950">Key</span></span>                     | <span data-ttu-id="dd6ad-951">Wartość</span><span class="sxs-lookup"><span data-stu-id="dd6ad-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="dd6ad-952">json_array: klucz</span><span class="sxs-lookup"><span data-stu-id="dd6ad-952">json_array:key</span></span>          | <span data-ttu-id="dd6ad-953">wartośća</span><span class="sxs-lookup"><span data-stu-id="dd6ad-953">valueA</span></span> |
| <span data-ttu-id="dd6ad-954">json_array: podsekcja: 0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-954">json_array:subsection:0</span></span> | <span data-ttu-id="dd6ad-955">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="dd6ad-955">valueB</span></span> |
| <span data-ttu-id="dd6ad-956">json_array: podsekcja: 1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-956">json_array:subsection:1</span></span> | <span data-ttu-id="dd6ad-957">valueC</span><span class="sxs-lookup"><span data-stu-id="dd6ad-957">valueC</span></span> |
| <span data-ttu-id="dd6ad-958">json_array: podsekcja: 2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-958">json_array:subsection:2</span></span> | <span data-ttu-id="dd6ad-959">Znajdując</span><span class="sxs-lookup"><span data-stu-id="dd6ad-959">valueD</span></span> |

<span data-ttu-id="dd6ad-960">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="dd6ad-961">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="dd6ad-962">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="dd6ad-963">`JsonArrayExample.Subsection` Indeks</span><span class="sxs-lookup"><span data-stu-id="dd6ad-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="dd6ad-964">`JsonArrayExample.Subsection` Wartościami</span><span class="sxs-lookup"><span data-stu-id="dd6ad-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="dd6ad-965">0</span><span class="sxs-lookup"><span data-stu-id="dd6ad-965">0</span></span>                                   | <span data-ttu-id="dd6ad-966">Wartośćb</span><span class="sxs-lookup"><span data-stu-id="dd6ad-966">valueB</span></span>                              |
| <span data-ttu-id="dd6ad-967">1</span><span class="sxs-lookup"><span data-stu-id="dd6ad-967">1</span></span>                                   | <span data-ttu-id="dd6ad-968">valueC</span><span class="sxs-lookup"><span data-stu-id="dd6ad-968">valueC</span></span>                              |
| <span data-ttu-id="dd6ad-969">2</span><span class="sxs-lookup"><span data-stu-id="dd6ad-969">2</span></span>                                   | <span data-ttu-id="dd6ad-970">Znajdując</span><span class="sxs-lookup"><span data-stu-id="dd6ad-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="dd6ad-971">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dd6ad-971">Custom configuration provider</span></span>

<span data-ttu-id="dd6ad-972">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="dd6ad-973">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="dd6ad-974">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="dd6ad-975">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="dd6ad-976">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="dd6ad-977">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="dd6ad-978">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="dd6ad-979">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="dd6ad-980">*Modele/EFConfigurationValue. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-980">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="dd6ad-981">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="dd6ad-982">*EFConfigurationProvider/EFConfigurationContext. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="dd6ad-983">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="dd6ad-984">*EFConfigurationProvider/EFConfigurationSource. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="dd6ad-985">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="dd6ad-986">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="dd6ad-987">*EFConfigurationProvider/EFConfigurationProvider. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="dd6ad-988">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="dd6ad-989">*Rozszerzenia/EntityFrameworkExtensions. cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-989">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="dd6ad-990">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="dd6ad-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="dd6ad-991">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="dd6ad-991">Access configuration during startup</span></span>

<span data-ttu-id="dd6ad-992">Wsuń `IConfiguration` do `Startup` konstruktora, aby uzyskać dostęp do wartości konfiguracyjnych w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dd6ad-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dd6ad-993">Aby uzyskać dostęp do konfiguracji w programie `Startup.Configure` , należy wstrzyknąć `IConfiguration` bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="dd6ad-994">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="dd6ad-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="dd6ad-995">Konfiguracja dostępu na :::no-loc(Razor)::: stronie stron lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="dd6ad-995">Access configuration in a :::no-loc(Razor)::: Pages page or MVC view</span></span>

<span data-ttu-id="dd6ad-996">Aby uzyskać dostęp do ustawień konfiguracji na :::no-loc(Razor)::: stronie stron lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [ przestrzeni nazwMicrosoft.Extensions.Configwersja](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-996">To access configuration settings in a :::no-loc(Razor)::: Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="dd6ad-997">Na :::no-loc(Razor)::: stronie stron:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-997">In a :::no-loc(Razor)::: Pages page:</span></span>

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
    <h1>Access configuration in a :::no-loc(Razor)::: Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="dd6ad-998">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="dd6ad-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="dd6ad-999">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="dd6ad-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="dd6ad-1000"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="dd6ad-1001">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dd6ad-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd6ad-1002">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="dd6ad-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end