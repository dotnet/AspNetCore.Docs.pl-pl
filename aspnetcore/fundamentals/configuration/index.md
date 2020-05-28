---
<span data-ttu-id="61923-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-102">'Blazor'</span></span>
- <span data-ttu-id="61923-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-103">'Identity'</span></span>
- <span data-ttu-id="61923-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-105">'Razor'</span></span>
- <span data-ttu-id="61923-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="61923-107">Konfiguracja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61923-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="61923-108">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="61923-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="61923-109">Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp).</span><span class="sxs-lookup"><span data-stu-id="61923-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="61923-110">Dostawcy konfiguracji odczytują dane konfiguracji z par klucz-wartość przy użyciu różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="61923-111">Pliki ustawień, takie jak *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="61923-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="61923-112">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="61923-112">Environment variables</span></span>
* <span data-ttu-id="61923-113">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="61923-113">Azure Key Vault</span></span>
* <span data-ttu-id="61923-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="61923-114">Azure App Configuration</span></span>
* <span data-ttu-id="61923-115">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-115">Command-line arguments</span></span>
* <span data-ttu-id="61923-116">Niestandardowi dostawcy, instalowani lub utworzony</span><span class="sxs-lookup"><span data-stu-id="61923-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="61923-117">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="61923-117">Directory files</span></span>
* <span data-ttu-id="61923-118">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="61923-118">In-memory .NET objects</span></span>

<span data-ttu-id="61923-119">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61923-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="61923-120">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="61923-120">Default configuration</span></span>

<span data-ttu-id="61923-121">ASP.NET Core aplikacje sieci Web utworzone za pomocą programu [dotnet New](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="61923-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="61923-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="61923-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="61923-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Dodaje istniejący element `IConfiguration` jako źródło.</span><span class="sxs-lookup"><span data-stu-id="61923-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="61923-124">W przypadku konfiguracji domyślnej program dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji_ .</span><span class="sxs-lookup"><span data-stu-id="61923-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="61923-125">[appSettings. JSON](#appsettingsjson) przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="61923-126">*appSettings.* `Environment` *. JSON* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="61923-127">Na przykład *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. *kod JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="61923-128">Wpisy [tajne aplikacji](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="61923-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="61923-129">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="61923-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="61923-130">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).</span><span class="sxs-lookup"><span data-stu-id="61923-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="61923-131">Dostawcy konfiguracji, którzy zostaną dodani później przesłaniają poprzednie ustawienia klucza.</span><span class="sxs-lookup"><span data-stu-id="61923-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="61923-132">Na przykład, jeśli `MyKey` jest ustawiony zarówno w pliku *appSettings. JSON* , jak i w środowisku, zostanie użyta wartość środowiska.</span><span class="sxs-lookup"><span data-stu-id="61923-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="61923-133">Przy użyciu domyślnych dostawców konfiguracji [dostawca konfiguracji wiersza polecenia](#command-line-configuration-provider) zastępuje wszystkich innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="61923-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="61923-134">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz [ustawienia domyślnego konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="61923-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="61923-135">Poniższy kod wyświetla dostawców konfiguracji włączonych w kolejności, w jakiej zostały dodane:</span><span class="sxs-lookup"><span data-stu-id="61923-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="61923-136">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="61923-136">appsettings.json</span></span>

<span data-ttu-id="61923-137">Rozważmy następujący plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="61923-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="61923-138">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-139">Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Konfiguracja ładowania w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="61923-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="61923-140">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="61923-140">*appsettings.json*</span></span>
1. <span data-ttu-id="61923-141">*appSettings.* `Environment` *. JSON* : na przykład, *AppSettings*. ***Środowisko produkcyjne***. *JSON* i *AppSettings*. ***Programowanie***. pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="61923-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="61923-142">Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="61923-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="61923-143">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="61923-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="61923-144">*AppSettings*. `Environment` . wartości *JSON* przesłaniają klucze w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="61923-145">Na przykład domyślnie:</span><span class="sxs-lookup"><span data-stu-id="61923-145">For example, by default:</span></span>

* <span data-ttu-id="61923-146">W obszarze programowanie, *AppSettings*. ***Programowanie***. Konfiguracja *JSON* zastępuje wartości Znalezione w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="61923-147">W obszarze produkcja, *AppSettings*. ***Środowisko produkcyjne***. Konfiguracja *JSON* zastępuje wartości Znalezione w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="61923-148">Na przykład podczas wdrażania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="61923-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="61923-149">Powiązywanie hierarchicznych danych konfiguracji przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="61923-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="61923-150">Korzystając z konfiguracji [domyślnej](#default) , pliku *appSettings. JSON* i *appSettings.* `Environment` pliki *. JSON* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="61923-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="61923-151">Zmiany wprowadzone w pliku *appSettings. JSON* i *appSettings.* `Environment` plik *. JSON* ***po*** uruchomieniu aplikacji jest odczytywany przez [dostawcę konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="61923-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="61923-152">Aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracji JSON, zobacz [dostawca konfiguracji JSON](#jcp) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="61923-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="61923-153">Security and Secret Manager</span><span class="sxs-lookup"><span data-stu-id="61923-153">Security and secret manager</span></span>

<span data-ttu-id="61923-154">Wskazówki dotyczące danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="61923-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="61923-155">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="61923-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="61923-156">Za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets) można przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="61923-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="61923-157">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="61923-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="61923-158">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="61923-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="61923-159">[Domyślnie](#default)program [Secret Manager](xref:security/app-secrets) odczytuje ustawienia konfiguracji po pliku *appSettings. JSON* i *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="61923-160">Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:</span><span class="sxs-lookup"><span data-stu-id="61923-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="61923-161"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="61923-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="61923-162">Menedżer wpisów tajnych używa [dostawcy konfiguracji plików](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="61923-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="61923-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="61923-164">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="61923-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="61923-165">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="61923-165">Environment variables</span></span>

<span data-ttu-id="61923-166">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość po odczytywaniu pliku *appSettings. JSON*, *appSettings.* `Environment` *. JSON*i [Menedżer wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="61923-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="61923-167">W związku z tym kluczowe wartości odczytywane z wartości zastąpienia środowiska są odczytywane z pliku *appSettings. JSON*, *appSettings.* `Environment` *. JSON*i Menedżer wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="61923-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="61923-168">Następujące `set` polecenia:</span><span class="sxs-lookup"><span data-stu-id="61923-168">The following `set` commands:</span></span>

* <span data-ttu-id="61923-169">Ustaw klucze środowiska i wartości z [poprzedniego przykładu](#appsettingsjson) w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="61923-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="61923-170">Przetestuj ustawienia przy użyciu pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="61923-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="61923-171">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="61923-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="61923-172">Poprzednie ustawienia środowiska:</span><span class="sxs-lookup"><span data-stu-id="61923-172">The preceding environment settings:</span></span>

* <span data-ttu-id="61923-173">Są ustawiane tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="61923-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="61923-174">Nie będą odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="61923-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="61923-175">Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy środowiskowych i wartości w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="61923-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="61923-176">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="61923-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="61923-177">`/M`ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="61923-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="61923-178">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="61923-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="61923-179">Aby sprawdzić, czy poprzednie polecenia przesłaniają plik *appSettings. JSON* i *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="61923-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="61923-180">Za pomocą programu Visual Studio: Zamknij i uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="61923-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="61923-181">Za pomocą interfejsu wiersza polecenia: Uruchom nowe okno poleceń i wprowadź `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="61923-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="61923-182">Połącz <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> z ciągiem, aby określić prefiks dla zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="61923-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="61923-183">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="61923-183">In the preceding code:</span></span>

* <span data-ttu-id="61923-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`zostanie dodany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="61923-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="61923-185">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="61923-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="61923-186">Zmienne środowiskowe ustawione z `MyCustomPrefix_` prefiksem przesłaniają [domyślnych dostawców konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="61923-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="61923-187">Obejmuje to zmienne środowiskowe bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="61923-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="61923-188">Prefiks jest usuwany, gdy pary klucz konfiguracji-wartość są odczytywane.</span><span class="sxs-lookup"><span data-stu-id="61923-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="61923-189">Następujące polecenia testują prefiks niestandardowy:</span><span class="sxs-lookup"><span data-stu-id="61923-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="61923-190">[Konfiguracja domyślna](#default) ładuje zmienne środowiskowe i argumenty wiersza polecenia poprzedzone `DOTNET_` i `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="61923-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="61923-191">`DOTNET_` `ASPNETCORE_` Prefiksy i są używane przez ASP.NET Core do [konfiguracji hosta i aplikacji](xref:fundamentals/host/generic-host#host-configuration), ale nie do konfiguracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="61923-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="61923-192">Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="61923-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="61923-193">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="61923-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="61923-194">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="61923-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="61923-195">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="61923-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="61923-196">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="61923-196">Exposed as environment variables.</span></span>

<span data-ttu-id="61923-197">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="61923-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="61923-198">Aby uzyskać informacje na temat parametrów połączenia z usługą Azure Database, zobacz [prefiksy parametrów połączenia](#constr) .</span><span class="sxs-lookup"><span data-stu-id="61923-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="61923-199">Wiersz polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-199">Command-line</span></span>

<span data-ttu-id="61923-200">Korzystając z konfiguracji [domyślnej](#default) , <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje konfigurację z par klucz-wartość argumentu wiersza polecenia po następujących źródłach konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="61923-201">*appSettings. JSON* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="61923-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="61923-202">Wpisy [tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="61923-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="61923-203">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="61923-203">Environment variables.</span></span>

<span data-ttu-id="61923-204">[Domyślnie](#default)wartości konfiguracji ustawione w wierszu polecenia przesłaniają wartości konfiguracyjne ustawione dla wszystkich innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="61923-205">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-205">Command-line arguments</span></span>

<span data-ttu-id="61923-206">Następujące polecenie ustawia klucze i wartości przy użyciu `=` :</span><span class="sxs-lookup"><span data-stu-id="61923-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="61923-207">Następujące polecenie ustawia klucze i wartości przy użyciu `/` :</span><span class="sxs-lookup"><span data-stu-id="61923-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="61923-208">Następujące polecenie ustawia klucze i wartości przy użyciu `--` :</span><span class="sxs-lookup"><span data-stu-id="61923-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="61923-209">Wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="61923-209">The key value:</span></span>

* <span data-ttu-id="61923-210">Musi `=` być zgodna lub musi mieć prefiks lub, gdy wartość znajduje się w `--` `/` miejscu.</span><span class="sxs-lookup"><span data-stu-id="61923-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="61923-211">Nie jest wymagane, jeśli `=` jest używany.</span><span class="sxs-lookup"><span data-stu-id="61923-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="61923-212">Na przykład `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="61923-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="61923-213">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które są używane `=` z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="61923-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="61923-214">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="61923-214">Switch mappings</span></span>

<span data-ttu-id="61923-215">Mapowania przełączników Zezwalaj na logikę zamiany nazwy **klucza** .</span><span class="sxs-lookup"><span data-stu-id="61923-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="61923-216">Udostępnienie słownika przemieszczenia przełączników w <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodzie.</span><span class="sxs-lookup"><span data-stu-id="61923-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="61923-217">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="61923-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="61923-218">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="61923-219">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="61923-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="61923-220">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="61923-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="61923-221">Przełączniki muszą zaczynać się od `-` lub `--` .</span><span class="sxs-lookup"><span data-stu-id="61923-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="61923-222">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="61923-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="61923-223">Aby użyć słownika mapowania przełączników, przekaż go do wywołania `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="61923-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="61923-224">Poniższy kod przedstawia wartości kluczy zastępowanych kluczy:</span><span class="sxs-lookup"><span data-stu-id="61923-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-225">Uruchom następujące polecenie, aby przetestować zastąpienie klucza:</span><span class="sxs-lookup"><span data-stu-id="61923-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="61923-226">Uwaga: obecnie `=` nie można użyć, aby ustawić wartości zastępcze klucza za pomocą pojedynczej kreski `-` .</span><span class="sxs-lookup"><span data-stu-id="61923-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="61923-227">Zobacz [ten problem](https://github.com/dotnet/extensions/issues/3059)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="61923-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="61923-228">Następujące polecenie działa w celu zastąpienia klucza testowego:</span><span class="sxs-lookup"><span data-stu-id="61923-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="61923-229">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="61923-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="61923-230">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="61923-231">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="61923-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="61923-232">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-232">Hierarchical configuration data</span></span>

<span data-ttu-id="61923-233">Interfejs API konfiguracji odczytuje hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="61923-234">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="61923-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="61923-235">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-236">Preferowanym sposobem odczytywania hierarchicznych danych konfiguracji jest użycie wzorca opcji.</span><span class="sxs-lookup"><span data-stu-id="61923-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="61923-237">Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracji](#optpat) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="61923-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="61923-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="61923-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="61923-239">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection).</span><span class="sxs-lookup"><span data-stu-id="61923-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="61923-240">Klucze i wartości konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-240">Configuration keys and values</span></span>

<span data-ttu-id="61923-241">Klucze konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-241">Configuration keys:</span></span>

* <span data-ttu-id="61923-242">Bez uwzględniania wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="61923-242">Are case-insensitive.</span></span> <span data-ttu-id="61923-243">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="61923-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="61923-244">Jeśli klucz i wartość są ustawione w więcej niż jednym dostawcy konfiguracji, zostanie użyta wartość z ostatniego dodawanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="61923-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="61923-245">Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).</span><span class="sxs-lookup"><span data-stu-id="61923-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="61923-246">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="61923-246">Hierarchical keys</span></span>
  * <span data-ttu-id="61923-247">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="61923-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="61923-248">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="61923-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="61923-249">Podwójne podkreślenie, `__` ,, jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek `:` .</span><span class="sxs-lookup"><span data-stu-id="61923-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="61923-250">W Azure Key Vault klucze hierarchiczne używają `--` jako separatora.</span><span class="sxs-lookup"><span data-stu-id="61923-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="61923-251">[Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) automatycznie zastępuje `--` przy użyciu `:` po załadowaniu wpisów tajnych do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="61923-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="61923-253">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#boa) .</span><span class="sxs-lookup"><span data-stu-id="61923-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="61923-254">Wartości konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-254">Configuration values:</span></span>

* <span data-ttu-id="61923-255">Są ciągami.</span><span class="sxs-lookup"><span data-stu-id="61923-255">Are strings.</span></span>
* <span data-ttu-id="61923-256">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="61923-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="61923-257">Dostawcy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-257">Configuration providers</span></span>

<span data-ttu-id="61923-258">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="61923-259">Dostawca</span><span class="sxs-lookup"><span data-stu-id="61923-259">Provider</span></span> | <span data-ttu-id="61923-260">Zapewnia konfigurację z</span><span class="sxs-lookup"><span data-stu-id="61923-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="61923-261">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-262">'Blazor'</span></span>
- <span data-ttu-id="61923-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-263">'Identity'</span></span>
- <span data-ttu-id="61923-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-265">'Razor'</span></span>
- <span data-ttu-id="61923-266">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-267">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-268">'Blazor'</span></span>
- <span data-ttu-id="61923-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-269">'Identity'</span></span>
- <span data-ttu-id="61923-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-271">'Razor'</span></span>
- <span data-ttu-id="61923-272">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-272">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-273">---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-274">'Blazor'</span></span>
- <span data-ttu-id="61923-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-275">'Identity'</span></span>
- <span data-ttu-id="61923-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-277">'Razor'</span></span>
- <span data-ttu-id="61923-278">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-279">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-280">'Blazor'</span></span>
- <span data-ttu-id="61923-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-281">'Identity'</span></span>
- <span data-ttu-id="61923-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-283">'Razor'</span></span>
- <span data-ttu-id="61923-284">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-285">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-286">'Blazor'</span></span>
- <span data-ttu-id="61923-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-287">'Identity'</span></span>
- <span data-ttu-id="61923-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-289">'Razor'</span></span>
- <span data-ttu-id="61923-290">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-291">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-292">'Blazor'</span></span>
- <span data-ttu-id="61923-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-293">'Identity'</span></span>
- <span data-ttu-id="61923-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-295">'Razor'</span></span>
- <span data-ttu-id="61923-296">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-297">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-298">'Blazor'</span></span>
- <span data-ttu-id="61923-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-299">'Identity'</span></span>
- <span data-ttu-id="61923-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-301">'Razor'</span></span>
- <span data-ttu-id="61923-302">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-303">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-304">'Blazor'</span></span>
- <span data-ttu-id="61923-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-305">'Identity'</span></span>
- <span data-ttu-id="61923-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-307">'Razor'</span></span>
- <span data-ttu-id="61923-308">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-309">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-310">'Blazor'</span></span>
- <span data-ttu-id="61923-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-311">'Identity'</span></span>
- <span data-ttu-id="61923-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-313">'Razor'</span></span>
- <span data-ttu-id="61923-314">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-315">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-316">'Blazor'</span></span>
- <span data-ttu-id="61923-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-317">'Identity'</span></span>
- <span data-ttu-id="61923-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-319">'Razor'</span></span>
- <span data-ttu-id="61923-320">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-321">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-322">'Blazor'</span></span>
- <span data-ttu-id="61923-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-323">'Identity'</span></span>
- <span data-ttu-id="61923-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-325">'Razor'</span></span>
- <span data-ttu-id="61923-326">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-327">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-328">'Blazor'</span></span>
- <span data-ttu-id="61923-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-329">'Identity'</span></span>
- <span data-ttu-id="61923-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-331">'Razor'</span></span>
- <span data-ttu-id="61923-332">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-333">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-334">'Blazor'</span></span>
- <span data-ttu-id="61923-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-335">'Identity'</span></span>
- <span data-ttu-id="61923-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-337">'Razor'</span></span>
- <span data-ttu-id="61923-338">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-339">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-340">'Blazor'</span></span>
- <span data-ttu-id="61923-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-341">'Identity'</span></span>
- <span data-ttu-id="61923-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-343">'Razor'</span></span>
- <span data-ttu-id="61923-344">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-345">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-346">'Blazor'</span></span>
- <span data-ttu-id="61923-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-347">'Identity'</span></span>
- <span data-ttu-id="61923-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-349">'Razor'</span></span>
- <span data-ttu-id="61923-350">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-351">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-352">'Blazor'</span></span>
- <span data-ttu-id="61923-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-353">'Identity'</span></span>
- <span data-ttu-id="61923-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-355">'Razor'</span></span>
- <span data-ttu-id="61923-356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-357">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-358">'Blazor'</span></span>
- <span data-ttu-id="61923-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-359">'Identity'</span></span>
- <span data-ttu-id="61923-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-361">'Razor'</span></span>
- <span data-ttu-id="61923-362">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-362">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-363">------------------ | | [Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Konfiguracja aplikacji platformy Azure | | [Dostawca konfiguracji wiersza polecenia](#clcp) | Parametry wiersza polecenia | | [Niestandardowy dostawca konfiguracji](#custom-configuration-provider) | Źródło niestandardowe | | [Dostawca konfiguracji zmiennych środowiskowych](#evcp) | Zmienne środowiskowe | | [Dostawca konfiguracji plików](#file-configuration-provider) | Pliki INI, JSON i XML | | [Dostawca konfiguracji klucza dla plików](#key-per-file-configuration-provider) | Pliki katalogu | | [Dostawca konfiguracji pamięci](#memory-configuration-provider) | Kolekcje w pamięci | | [Secret Manager](xref:security/app-secrets) | Plik w katalogu profilu użytkownika |</span><span class="sxs-lookup"><span data-stu-id="61923-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="61923-364">Źródła konfiguracji są odczytywane w kolejności, w jakiej zostały określone dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="61923-365">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="61923-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="61923-366">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="61923-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="61923-367">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="61923-367">*appsettings.json*</span></span>
1. <span data-ttu-id="61923-368">*AppSettings*. `Environment` . *kod JSON*</span><span class="sxs-lookup"><span data-stu-id="61923-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="61923-369">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="61923-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="61923-370">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#evcp).</span><span class="sxs-lookup"><span data-stu-id="61923-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="61923-371">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="61923-372">Typowym celem jest dodanie dostawcy konfiguracji wiersza polecenia w ciągu kilku dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="61923-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="61923-373">Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).</span><span class="sxs-lookup"><span data-stu-id="61923-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="61923-374">Prefiksy parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="61923-374">Connection string prefixes</span></span>

<span data-ttu-id="61923-375">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="61923-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="61923-376">Te parametry połączenia są związane z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="61923-377">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji z [konfiguracją domyślną](#default) lub gdy nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="61923-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="61923-378">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="61923-378">Connection string prefix</span></span> | <span data-ttu-id="61923-379">Dostawca</span><span class="sxs-lookup"><span data-stu-id="61923-379">Provider</span></span> |
| ---
<span data-ttu-id="61923-380">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-381">'Blazor'</span></span>
- <span data-ttu-id="61923-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-382">'Identity'</span></span>
- <span data-ttu-id="61923-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-384">'Razor'</span></span>
- <span data-ttu-id="61923-385">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-386">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-387">'Blazor'</span></span>
- <span data-ttu-id="61923-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-388">'Identity'</span></span>
- <span data-ttu-id="61923-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-390">'Razor'</span></span>
- <span data-ttu-id="61923-391">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-392">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-393">'Blazor'</span></span>
- <span data-ttu-id="61923-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-394">'Identity'</span></span>
- <span data-ttu-id="61923-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-396">'Razor'</span></span>
- <span data-ttu-id="61923-397">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-398">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-399">'Blazor'</span></span>
- <span data-ttu-id="61923-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-400">'Identity'</span></span>
- <span data-ttu-id="61923-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-402">'Razor'</span></span>
- <span data-ttu-id="61923-403">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-404">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-405">'Blazor'</span></span>
- <span data-ttu-id="61923-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-406">'Identity'</span></span>
- <span data-ttu-id="61923-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-408">'Razor'</span></span>
- <span data-ttu-id="61923-409">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-410">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-411">'Blazor'</span></span>
- <span data-ttu-id="61923-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-412">'Identity'</span></span>
- <span data-ttu-id="61923-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-414">'Razor'</span></span>
- <span data-ttu-id="61923-415">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-416">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-417">'Blazor'</span></span>
- <span data-ttu-id="61923-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-418">'Identity'</span></span>
- <span data-ttu-id="61923-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-420">'Razor'</span></span>
- <span data-ttu-id="61923-421">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-422">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-423">'Blazor'</span></span>
- <span data-ttu-id="61923-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-424">'Identity'</span></span>
- <span data-ttu-id="61923-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-426">'Razor'</span></span>
- <span data-ttu-id="61923-427">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-428">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-429">'Blazor'</span></span>
- <span data-ttu-id="61923-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-430">'Identity'</span></span>
- <span data-ttu-id="61923-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-432">'Razor'</span></span>
- <span data-ttu-id="61923-433">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-434">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-435">'Blazor'</span></span>
- <span data-ttu-id="61923-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-436">'Identity'</span></span>
- <span data-ttu-id="61923-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-438">'Razor'</span></span>
- <span data-ttu-id="61923-439">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-439">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-440">------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-441">'Blazor'</span></span>
- <span data-ttu-id="61923-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-442">'Identity'</span></span>
- <span data-ttu-id="61923-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-444">'Razor'</span></span>
- <span data-ttu-id="61923-445">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-446">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-447">'Blazor'</span></span>
- <span data-ttu-id="61923-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-448">'Identity'</span></span>
- <span data-ttu-id="61923-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-450">'Razor'</span></span>
- <span data-ttu-id="61923-451">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-451">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-452">---- | | `CUSTOMCONNSTR_` | Dostawca niestandardowy | | `MYSQLCONNSTR_` | [Baza danych MySQL](https://www.mysql.com/) |
 | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="61923-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="61923-453">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="61923-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="61923-454">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="61923-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="61923-455">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="61923-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="61923-456">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="61923-456">Environment variable key</span></span> | <span data-ttu-id="61923-457">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-457">Converted configuration key</span></span> | <span data-ttu-id="61923-458">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="61923-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="61923-459">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-460">'Blazor'</span></span>
- <span data-ttu-id="61923-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-461">'Identity'</span></span>
- <span data-ttu-id="61923-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-463">'Razor'</span></span>
- <span data-ttu-id="61923-464">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-465">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-466">'Blazor'</span></span>
- <span data-ttu-id="61923-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-467">'Identity'</span></span>
- <span data-ttu-id="61923-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-469">'Razor'</span></span>
- <span data-ttu-id="61923-470">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-471">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-472">'Blazor'</span></span>
- <span data-ttu-id="61923-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-473">'Identity'</span></span>
- <span data-ttu-id="61923-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-475">'Razor'</span></span>
- <span data-ttu-id="61923-476">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-477">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-478">'Blazor'</span></span>
- <span data-ttu-id="61923-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-479">'Identity'</span></span>
- <span data-ttu-id="61923-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-481">'Razor'</span></span>
- <span data-ttu-id="61923-482">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-483">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-484">'Blazor'</span></span>
- <span data-ttu-id="61923-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-485">'Identity'</span></span>
- <span data-ttu-id="61923-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-487">'Razor'</span></span>
- <span data-ttu-id="61923-488">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-489">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-490">'Blazor'</span></span>
- <span data-ttu-id="61923-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-491">'Identity'</span></span>
- <span data-ttu-id="61923-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-493">'Razor'</span></span>
- <span data-ttu-id="61923-494">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-495">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-496">'Blazor'</span></span>
- <span data-ttu-id="61923-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-497">'Identity'</span></span>
- <span data-ttu-id="61923-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-499">'Razor'</span></span>
- <span data-ttu-id="61923-500">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-501">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-502">'Blazor'</span></span>
- <span data-ttu-id="61923-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-503">'Identity'</span></span>
- <span data-ttu-id="61923-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-505">'Razor'</span></span>
- <span data-ttu-id="61923-506">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-507">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-508">'Blazor'</span></span>
- <span data-ttu-id="61923-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-509">'Identity'</span></span>
- <span data-ttu-id="61923-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-511">'Razor'</span></span>
- <span data-ttu-id="61923-512">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-513">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-514">'Blazor'</span></span>
- <span data-ttu-id="61923-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-515">'Identity'</span></span>
- <span data-ttu-id="61923-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-517">'Razor'</span></span>
- <span data-ttu-id="61923-518">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-518">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-519">------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-520">'Blazor'</span></span>
- <span data-ttu-id="61923-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-521">'Identity'</span></span>
- <span data-ttu-id="61923-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-523">'Razor'</span></span>
- <span data-ttu-id="61923-524">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-525">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-526">'Blazor'</span></span>
- <span data-ttu-id="61923-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-527">'Identity'</span></span>
- <span data-ttu-id="61923-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-529">'Razor'</span></span>
- <span data-ttu-id="61923-530">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-531">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-532">'Blazor'</span></span>
- <span data-ttu-id="61923-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-533">'Identity'</span></span>
- <span data-ttu-id="61923-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-535">'Razor'</span></span>
- <span data-ttu-id="61923-536">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-537">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-538">'Blazor'</span></span>
- <span data-ttu-id="61923-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-539">'Identity'</span></span>
- <span data-ttu-id="61923-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-541">'Razor'</span></span>
- <span data-ttu-id="61923-542">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-543">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-544">'Blazor'</span></span>
- <span data-ttu-id="61923-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-545">'Identity'</span></span>
- <span data-ttu-id="61923-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-547">'Razor'</span></span>
- <span data-ttu-id="61923-548">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-549">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-550">'Blazor'</span></span>
- <span data-ttu-id="61923-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-551">'Identity'</span></span>
- <span data-ttu-id="61923-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-553">'Razor'</span></span>
- <span data-ttu-id="61923-554">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-555">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-556">'Blazor'</span></span>
- <span data-ttu-id="61923-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-557">'Identity'</span></span>
- <span data-ttu-id="61923-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-559">'Razor'</span></span>
- <span data-ttu-id="61923-560">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-561">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-562">'Blazor'</span></span>
- <span data-ttu-id="61923-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-563">'Identity'</span></span>
- <span data-ttu-id="61923-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-565">'Razor'</span></span>
- <span data-ttu-id="61923-566">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-567">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-568">'Blazor'</span></span>
- <span data-ttu-id="61923-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-569">'Identity'</span></span>
- <span data-ttu-id="61923-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-571">'Razor'</span></span>
- <span data-ttu-id="61923-572">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-573">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-574">'Blazor'</span></span>
- <span data-ttu-id="61923-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-575">'Identity'</span></span>
- <span data-ttu-id="61923-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-577">'Razor'</span></span>
- <span data-ttu-id="61923-578">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-579">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-580">'Blazor'</span></span>
- <span data-ttu-id="61923-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-581">'Identity'</span></span>
- <span data-ttu-id="61923-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-583">'Razor'</span></span>
- <span data-ttu-id="61923-584">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-584">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-585">-------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-586">'Blazor'</span></span>
- <span data-ttu-id="61923-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-587">'Identity'</span></span>
- <span data-ttu-id="61923-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-589">'Razor'</span></span>
- <span data-ttu-id="61923-590">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-591">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-592">'Blazor'</span></span>
- <span data-ttu-id="61923-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-593">'Identity'</span></span>
- <span data-ttu-id="61923-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-595">'Razor'</span></span>
- <span data-ttu-id="61923-596">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-597">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-598">'Blazor'</span></span>
- <span data-ttu-id="61923-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-599">'Identity'</span></span>
- <span data-ttu-id="61923-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-601">'Razor'</span></span>
- <span data-ttu-id="61923-602">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-603">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-604">'Blazor'</span></span>
- <span data-ttu-id="61923-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-605">'Identity'</span></span>
- <span data-ttu-id="61923-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-607">'Razor'</span></span>
- <span data-ttu-id="61923-608">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-609">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-610">'Blazor'</span></span>
- <span data-ttu-id="61923-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-611">'Identity'</span></span>
- <span data-ttu-id="61923-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-613">'Razor'</span></span>
- <span data-ttu-id="61923-614">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-615">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-616">'Blazor'</span></span>
- <span data-ttu-id="61923-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-617">'Identity'</span></span>
- <span data-ttu-id="61923-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-619">'Razor'</span></span>
- <span data-ttu-id="61923-620">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-621">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-622">'Blazor'</span></span>
- <span data-ttu-id="61923-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-623">'Identity'</span></span>
- <span data-ttu-id="61923-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-625">'Razor'</span></span>
- <span data-ttu-id="61923-626">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-627">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-628">'Blazor'</span></span>
- <span data-ttu-id="61923-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-629">'Identity'</span></span>
- <span data-ttu-id="61923-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-631">'Razor'</span></span>
- <span data-ttu-id="61923-632">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-633">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-634">'Blazor'</span></span>
- <span data-ttu-id="61923-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-635">'Identity'</span></span>
- <span data-ttu-id="61923-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-637">'Razor'</span></span>
- <span data-ttu-id="61923-638">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-639">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-640">'Blazor'</span></span>
- <span data-ttu-id="61923-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-641">'Identity'</span></span>
- <span data-ttu-id="61923-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-643">'Razor'</span></span>
- <span data-ttu-id="61923-644">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-645">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-646">'Blazor'</span></span>
- <span data-ttu-id="61923-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-647">'Identity'</span></span>
- <span data-ttu-id="61923-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-649">'Razor'</span></span>
- <span data-ttu-id="61923-650">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-651">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-652">'Blazor'</span></span>
- <span data-ttu-id="61923-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-653">'Identity'</span></span>
- <span data-ttu-id="61923-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-655">'Razor'</span></span>
- <span data-ttu-id="61923-656">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-657">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-658">'Blazor'</span></span>
- <span data-ttu-id="61923-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-659">'Identity'</span></span>
- <span data-ttu-id="61923-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-661">'Razor'</span></span>
- <span data-ttu-id="61923-662">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-663">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-664">'Blazor'</span></span>
- <span data-ttu-id="61923-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-665">'Identity'</span></span>
- <span data-ttu-id="61923-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-667">'Razor'</span></span>
- <span data-ttu-id="61923-668">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-669">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-670">'Blazor'</span></span>
- <span data-ttu-id="61923-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-671">'Identity'</span></span>
- <span data-ttu-id="61923-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-673">'Razor'</span></span>
- <span data-ttu-id="61923-674">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-675">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-676">'Blazor'</span></span>
- <span data-ttu-id="61923-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-677">'Identity'</span></span>
- <span data-ttu-id="61923-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-679">'Razor'</span></span>
- <span data-ttu-id="61923-680">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-681">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-682">'Blazor'</span></span>
- <span data-ttu-id="61923-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-683">'Identity'</span></span>
- <span data-ttu-id="61923-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-685">'Razor'</span></span>
- <span data-ttu-id="61923-686">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-687">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-688">'Blazor'</span></span>
- <span data-ttu-id="61923-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-689">'Identity'</span></span>
- <span data-ttu-id="61923-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-691">'Razor'</span></span>
- <span data-ttu-id="61923-692">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-693">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-694">'Blazor'</span></span>
- <span data-ttu-id="61923-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-695">'Identity'</span></span>
- <span data-ttu-id="61923-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-697">'Razor'</span></span>
- <span data-ttu-id="61923-698">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-699">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-700">'Blazor'</span></span>
- <span data-ttu-id="61923-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-701">'Identity'</span></span>
- <span data-ttu-id="61923-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-703">'Razor'</span></span>
- <span data-ttu-id="61923-704">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-705">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-706">'Blazor'</span></span>
- <span data-ttu-id="61923-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-707">'Identity'</span></span>
- <span data-ttu-id="61923-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-709">'Razor'</span></span>
- <span data-ttu-id="61923-710">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-711">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-712">'Blazor'</span></span>
- <span data-ttu-id="61923-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-713">'Identity'</span></span>
- <span data-ttu-id="61923-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-715">'Razor'</span></span>
- <span data-ttu-id="61923-716">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-717">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-718">'Blazor'</span></span>
- <span data-ttu-id="61923-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-719">'Identity'</span></span>
- <span data-ttu-id="61923-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-721">'Razor'</span></span>
- <span data-ttu-id="61923-722">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-723">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-724">'Blazor'</span></span>
- <span data-ttu-id="61923-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-725">'Identity'</span></span>
- <span data-ttu-id="61923-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-727">'Razor'</span></span>
- <span data-ttu-id="61923-728">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-729">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-730">'Blazor'</span></span>
- <span data-ttu-id="61923-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-731">'Identity'</span></span>
- <span data-ttu-id="61923-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-733">'Razor'</span></span>
- <span data-ttu-id="61923-734">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-735">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-736">'Blazor'</span></span>
- <span data-ttu-id="61923-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-737">'Identity'</span></span>
- <span data-ttu-id="61923-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-739">'Razor'</span></span>
- <span data-ttu-id="61923-740">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-741">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-742">'Blazor'</span></span>
- <span data-ttu-id="61923-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-743">'Identity'</span></span>
- <span data-ttu-id="61923-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-745">'Razor'</span></span>
- <span data-ttu-id="61923-746">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-747">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-748">'Blazor'</span></span>
- <span data-ttu-id="61923-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-749">'Identity'</span></span>
- <span data-ttu-id="61923-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-751">'Razor'</span></span>
- <span data-ttu-id="61923-752">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-753">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-754">'Blazor'</span></span>
- <span data-ttu-id="61923-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-755">'Identity'</span></span>
- <span data-ttu-id="61923-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-757">'Razor'</span></span>
- <span data-ttu-id="61923-758">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-759">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-760">'Blazor'</span></span>
- <span data-ttu-id="61923-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-761">'Identity'</span></span>
- <span data-ttu-id="61923-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-763">'Razor'</span></span>
- <span data-ttu-id="61923-764">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-765">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-766">'Blazor'</span></span>
- <span data-ttu-id="61923-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-767">'Identity'</span></span>
- <span data-ttu-id="61923-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-769">'Razor'</span></span>
- <span data-ttu-id="61923-770">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-771">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-772">'Blazor'</span></span>
- <span data-ttu-id="61923-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-773">'Identity'</span></span>
- <span data-ttu-id="61923-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-775">'Razor'</span></span>
- <span data-ttu-id="61923-776">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-777">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-778">'Blazor'</span></span>
- <span data-ttu-id="61923-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-779">'Identity'</span></span>
- <span data-ttu-id="61923-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-781">'Razor'</span></span>
- <span data-ttu-id="61923-782">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-783">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-784">'Blazor'</span></span>
- <span data-ttu-id="61923-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-785">'Identity'</span></span>
- <span data-ttu-id="61923-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-787">'Razor'</span></span>
- <span data-ttu-id="61923-788">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-789">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-790">'Blazor'</span></span>
- <span data-ttu-id="61923-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-791">'Identity'</span></span>
- <span data-ttu-id="61923-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-793">'Razor'</span></span>
- <span data-ttu-id="61923-794">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-795">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-796">'Blazor'</span></span>
- <span data-ttu-id="61923-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-797">'Identity'</span></span>
- <span data-ttu-id="61923-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-799">'Razor'</span></span>
- <span data-ttu-id="61923-800">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-801">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-802">'Blazor'</span></span>
- <span data-ttu-id="61923-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-803">'Identity'</span></span>
- <span data-ttu-id="61923-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-805">'Razor'</span></span>
- <span data-ttu-id="61923-806">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-806">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="61923-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="61923-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="61923-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="61923-809">Wartość: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="61923-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="61923-810">Wartość: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="61923-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="61923-811">Wartościami`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="61923-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="61923-812">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="61923-812">JSON configuration provider</span></span>

<span data-ttu-id="61923-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON.</span><span class="sxs-lookup"><span data-stu-id="61923-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="61923-814">Przeciążenia mogą określać:</span><span class="sxs-lookup"><span data-stu-id="61923-814">Overloads can specify:</span></span>

* <span data-ttu-id="61923-815">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="61923-815">Whether the file is optional.</span></span>
* <span data-ttu-id="61923-816">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="61923-817">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="61923-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="61923-818">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="61923-818">The preceding code:</span></span>

* <span data-ttu-id="61923-819">Konfiguruje dostawcę konfiguracji JSON w celu załadowania pliku *. JSON* z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="61923-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="61923-820">`optional: true`: Plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="61923-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="61923-821">`reloadOnChange: true`: Plik zostanie ponownie załadowany podczas zapisywania zmian.</span><span class="sxs-lookup"><span data-stu-id="61923-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="61923-822">Odczytuje [domyślnych dostawców konfiguracji](#default) przed plikiem moja *config. JSON* .</span><span class="sxs-lookup"><span data-stu-id="61923-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="61923-823">Ustawienia w pliku *config. JSON* przesłaniają ustawienie w domyślnych dostawcach konfiguracji, w tym [dostawcę konfiguracji zmienne środowiskowe](#evcp) i [dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="61923-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="61923-824">Zwykle ***nie*** chcesz, aby niestandardowy plik JSON zastępujący wartości ustawione w [zmiennej środowiskowej dostawcy konfiguracji](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="61923-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="61923-825">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="61923-826">W powyższym kodzie ustawienia w *pliku config. JSON* i *konfiguracji*. `Environment` . pliki *JSON* :</span><span class="sxs-lookup"><span data-stu-id="61923-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="61923-827">Zastąp ustawienia w pliku *appSettings. JSON* i *AppSettings*. `Environment` . pliki *JSON* .</span><span class="sxs-lookup"><span data-stu-id="61923-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="61923-828">Są zastępowane przez ustawienia [dostawcy konfiguracji zmiennych środowiskowych](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="61923-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="61923-829">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *. JSON* :</span><span class="sxs-lookup"><span data-stu-id="61923-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="61923-830">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="61923-831">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="61923-831">File configuration provider</span></span>

<span data-ttu-id="61923-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="61923-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="61923-833">Następujący dostawcy konfiguracji pochodzą z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="61923-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="61923-834">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="61923-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="61923-835">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="61923-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="61923-836">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="61923-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="61923-837">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="61923-837">INI configuration provider</span></span>

<span data-ttu-id="61923-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="61923-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="61923-839">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="61923-840">W powyższym kodzie ustawienia w *MyIniConfig. ini* i *MyIniConfig*. `Environment` . pliki *ini* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="61923-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="61923-841">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="61923-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="61923-842">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="61923-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="61923-843">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="61923-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="61923-844">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="61923-845">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="61923-845">XML configuration provider</span></span>

<span data-ttu-id="61923-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="61923-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="61923-847">Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="61923-848">W powyższym kodzie ustawienia w *MyXMLFile. XML* i *MyXMLFile*. `Environment` . pliki *XML* są zastępowane przez ustawienia w:</span><span class="sxs-lookup"><span data-stu-id="61923-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="61923-849">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="61923-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="61923-850">[Dostawca konfiguracji wiersza polecenia](#clcp).</span><span class="sxs-lookup"><span data-stu-id="61923-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="61923-851">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="61923-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="61923-852">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka powyższych ustawień konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-853">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="61923-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="61923-854">Poniższy kod odczytuje poprzedni plik konfiguracji i wyświetla klucze i wartości:</span><span class="sxs-lookup"><span data-stu-id="61923-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-855">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="61923-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="61923-856">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="61923-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="61923-857">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="61923-857">key:attribute</span></span>
* <span data-ttu-id="61923-858">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="61923-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="61923-859">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="61923-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="61923-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="61923-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="61923-861">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-861">The key is the file name.</span></span> <span data-ttu-id="61923-862">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-862">The value contains the file's contents.</span></span> <span data-ttu-id="61923-863">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="61923-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="61923-864">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="61923-865">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="61923-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="61923-866">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="61923-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="61923-867">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="61923-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="61923-868">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="61923-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="61923-869">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="61923-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="61923-870">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="61923-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="61923-871">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="61923-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="61923-872">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="61923-872">Memory configuration provider</span></span>

<span data-ttu-id="61923-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="61923-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="61923-874">Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="61923-875">Poniższy kod z [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-876">W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawcach konfiguracji](#default).</span><span class="sxs-lookup"><span data-stu-id="61923-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="61923-877">Przykład określania kolejności dostawców konfiguracji można znaleźć w temacie [dostawca konfiguracji JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="61923-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="61923-878">Zobacz [Powiąż tablicę](#boa) z innym przykładem przy użyciu `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="61923-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="61923-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="61923-879">GetValue</span></span>

<span data-ttu-id="61923-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ:</span><span class="sxs-lookup"><span data-stu-id="61923-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-881">W powyższym kodzie, jeśli `NumberKey` nie zostanie znaleziony w konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="61923-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="61923-882">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="61923-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="61923-883">W poniższych przykładach należy wziąć pod uwagę następujący plik *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="61923-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="61923-884">Poniższy kod dodaje *MySubsection. JSON* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="61923-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="61923-885">GetSection</span></span>

<span data-ttu-id="61923-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="61923-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="61923-887">Poniższy kod zwraca wartości dla `section1` :</span><span class="sxs-lookup"><span data-stu-id="61923-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-888">Poniższy kod zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="61923-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-889">`GetSection`nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="61923-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="61923-890">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="61923-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="61923-891">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="61923-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="61923-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="61923-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="61923-893">GetChildren i istnieje</span><span class="sxs-lookup"><span data-stu-id="61923-893">GetChildren and Exists</span></span>

<span data-ttu-id="61923-894">Poniższy kod wywołuje [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) i zwraca wartości dla `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="61923-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-895">Poprzedni kod wywołuje [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby sprawdzić, czy sekcja istnieje:</span><span class="sxs-lookup"><span data-stu-id="61923-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="61923-896">Powiąż tablicę</span><span class="sxs-lookup"><span data-stu-id="61923-896">Bind an array</span></span>

<span data-ttu-id="61923-897">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="61923-898">Każdy format tablicy, który ujawnia segment klucza numerycznego, jest w stanie powiązać powiązanie tablicy z tablicą klas [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="61923-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="61923-899">Weź pod uwagę plik *webarray. JSON* z [przykładowego pobrania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="61923-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="61923-900">Poniższy kod dodaje plik *webarray. JSON* do dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="61923-901">Poniższy kod odczytuje konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="61923-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-902">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="61923-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="61923-903">W poprzednich danych wyjściowych indeks 3 ma wartość `value40` odpowiadającą `"4": "value40",` w pliku *webarray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="61923-904">Powiązane indeksy tablicy są ciągłe i nie są powiązane z indeksem klucza konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="61923-905">Obiekt tworzący konfigurację nie jest w stanie powiązać wartości null ani tworzyć wpisów o wartości null dla obiektów powiązanych</span><span class="sxs-lookup"><span data-stu-id="61923-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="61923-906">Poniższy kod ładuje `array:entries` konfigurację z użyciem <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="61923-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="61923-907">Poniższy kod odczytuje konfigurację w `arrayDict` `Dictionary` i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="61923-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-908">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="61923-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="61923-909">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="61923-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="61923-910">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="61923-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="61923-911">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="61923-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="61923-912">Brakujący element konfiguracji dla indeksu &num; 3 można dostarczyć przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje &num; parę klucz/wartość indeksu 3.</span><span class="sxs-lookup"><span data-stu-id="61923-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="61923-913">Rozważmy następujący plik *Wartość3. JSON* z przykładowego pobrania:</span><span class="sxs-lookup"><span data-stu-id="61923-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="61923-914">Poniższy kod zawiera konfigurację dla *Wartość3. JSON* i `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="61923-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="61923-915">Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:</span><span class="sxs-lookup"><span data-stu-id="61923-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-916">Poprzedni kod zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="61923-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="61923-917">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="61923-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="61923-918">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-918">Custom configuration provider</span></span>

<span data-ttu-id="61923-919">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="61923-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="61923-920">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="61923-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="61923-921">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="61923-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="61923-922">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="61923-923">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="61923-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="61923-924">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="61923-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="61923-925">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="61923-926">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="61923-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="61923-927">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="61923-928">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="61923-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="61923-929">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="61923-930">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="61923-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="61923-931">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="61923-932">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="61923-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="61923-933">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="61923-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="61923-934">Ponieważ w [kluczach konfiguracji jest rozróżniana wielkość liter](#keys), słownik używany do inicjowania bazy danych jest tworzony przy użyciu funkcji porównującej bez uwzględniania wielkości liter ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="61923-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="61923-935">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="61923-936">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="61923-937">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="61923-938">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="61923-939">Konfiguracja dostępu w programie startowym</span><span class="sxs-lookup"><span data-stu-id="61923-939">Access configuration in Startup</span></span>

<span data-ttu-id="61923-940">Poniższy kod przedstawia dane konfiguracji w `Startup` metodach:</span><span class="sxs-lookup"><span data-stu-id="61923-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="61923-941">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="61923-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="61923-942">Konfiguracja dostępu na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="61923-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="61923-943">Poniższy kod przedstawia dane konfiguracji na Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="61923-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="61923-944">W poniższym kodzie `MyOptions` został dodany do kontenera usługi z <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> i powiązana z konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="61923-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="61923-945">Następujące oznakowanie używa [`@inject`](xref:mvc/views/razor#inject) Razor dyrektywy do rozwiązywania i wyświetlania wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="61923-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="61923-946">Konfiguracja dostępu w pliku widoku MVC</span><span class="sxs-lookup"><span data-stu-id="61923-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="61923-947">Poniższy kod przedstawia dane konfiguracji w widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="61923-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="61923-948">Konfigurowanie opcji za pomocą delegata</span><span class="sxs-lookup"><span data-stu-id="61923-948">Configure options with a delegate</span></span>

<span data-ttu-id="61923-949">Opcje skonfigurowane w wartościach zastąpień delegatów ustawionych w dostawcach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="61923-950">Konfigurowanie opcji za pomocą delegata jest zademonstrowane jako przykład 2 w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="61923-951">W poniższym kodzie <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Usługa jest dodawana do kontenera usługi.</span><span class="sxs-lookup"><span data-stu-id="61923-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="61923-952">Używa delegata do konfigurowania wartości dla `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="61923-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="61923-953">Poniższy kod wyświetla wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="61923-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="61923-954">W poprzednim przykładzie wartości `Option1` i `Option2` są określone w pliku *appSettings. JSON* , a następnie zastąpione przez skonfigurowany delegat.</span><span class="sxs-lookup"><span data-stu-id="61923-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="61923-955">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="61923-955">Host versus app configuration</span></span>

<span data-ttu-id="61923-956">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="61923-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="61923-957">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="61923-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="61923-958">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="61923-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="61923-959">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="61923-960">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="61923-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="61923-961">Domyślna konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="61923-961">Default host configuration</span></span>

<span data-ttu-id="61923-962">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host), zobacz [wersję ASP.NET Core 2,2 tego tematu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="61923-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="61923-963">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="61923-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="61923-964">Zmienne środowiskowe poprzedzone znakiem `DOTNET_` (na przykład `DOTNET_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="61923-965">Prefiks ( `DOTNET_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="61923-966">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="61923-967">Konfiguracja domyślna hosta sieci Web ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="61923-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="61923-968">Kestrel jest używany jako serwer sieci Web i konfigurowany przy użyciu dostawców konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="61923-969">Dodaj oprogramowanie pośredniczące do filtrowania hosta.</span><span class="sxs-lookup"><span data-stu-id="61923-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="61923-970">Dodaj przekierowane nagłówki — oprogramowanie pośredniczące, jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` zmienna środowiskowa jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="61923-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="61923-971">Włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="61923-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="61923-972">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="61923-972">Other configuration</span></span>

<span data-ttu-id="61923-973">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="61923-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="61923-974">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="61923-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="61923-975">plik *Launch. JSON* / *profilu launchsettings. JSON* to pliki konfiguracji narzędzi dla środowiska programistycznego, opisane w temacie:</span><span class="sxs-lookup"><span data-stu-id="61923-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="61923-976">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="61923-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="61923-977">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="61923-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="61923-978">*Web. config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="61923-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="61923-979">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="61923-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="61923-980">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="61923-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="61923-981"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="61923-982">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="61923-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61923-983">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="61923-983">Additional resources</span></span>

* [<span data-ttu-id="61923-984">Kod źródłowy konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61923-985">Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość określonych przez *dostawców konfiguracji*.</span><span class="sxs-lookup"><span data-stu-id="61923-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="61923-986">Dostawcy konfiguracji odczytują dane konfiguracji do par klucz-wartość z różnych źródeł konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="61923-987">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="61923-987">Azure Key Vault</span></span>
* <span data-ttu-id="61923-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="61923-988">Azure App Configuration</span></span>
* <span data-ttu-id="61923-989">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-989">Command-line arguments</span></span>
* <span data-ttu-id="61923-990">Dostawcy niestandardowi (instalowani lub utworzony)</span><span class="sxs-lookup"><span data-stu-id="61923-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="61923-991">Pliki katalogu</span><span class="sxs-lookup"><span data-stu-id="61923-991">Directory files</span></span>
* <span data-ttu-id="61923-992">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="61923-992">Environment variables</span></span>
* <span data-ttu-id="61923-993">Obiekty w pamięci .NET</span><span class="sxs-lookup"><span data-stu-id="61923-993">In-memory .NET objects</span></span>
* <span data-ttu-id="61923-994">Pliki ustawień</span><span class="sxs-lookup"><span data-stu-id="61923-994">Settings files</span></span>

<span data-ttu-id="61923-995">Pakiety konfiguracyjne dla typowych scenariuszy dostawcy konfiguracji ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="61923-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="61923-996">Przykłady kodu, które obserwują i w przykładowej aplikacji używają <xref:Microsoft.Extensions.Configuration> przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="61923-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="61923-997">*Wzorzec opcji* jest rozszerzeniem pojęć konfiguracyjnych opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="61923-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="61923-998">Opcje używają klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="61923-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="61923-999">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="61923-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="61923-1000">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61923-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="61923-1001">Host a konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="61923-1001">Host versus app configuration</span></span>

<span data-ttu-id="61923-1002">Przed skonfigurowaniem i uruchomieniem aplikacji *host* zostanie skonfigurowany i uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="61923-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="61923-1003">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="61923-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="61923-1004">Zarówno aplikacja, jak i Host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="61923-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="61923-1005">Klucz konfiguracji hosta — pary wartości są również uwzględnione w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="61923-1006">Aby uzyskać więcej informacji na temat tego, jak dostawcy konfiguracji są używani podczas kompilowania hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="61923-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="61923-1007">Inna konfiguracja</span><span class="sxs-lookup"><span data-stu-id="61923-1007">Other configuration</span></span>

<span data-ttu-id="61923-1008">Ten temat dotyczy tylko *konfiguracji aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="61923-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="61923-1009">Inne aspekty uruchamiania i hostowania aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracji nieuwzględnionych w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="61923-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="61923-1010">plik *Launch. JSON* / *profilu launchsettings. JSON* to pliki konfiguracji narzędzi dla środowiska programistycznego, opisane w temacie:</span><span class="sxs-lookup"><span data-stu-id="61923-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="61923-1011">W programie <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="61923-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="61923-1012">W zestawie dokumentacji, w której pliki są używane do konfigurowania ASP.NET Core aplikacji na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="61923-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="61923-1013">*Web. config* to plik konfiguracji serwera opisany w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="61923-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="61923-1014">Aby uzyskać więcej informacji na temat migrowania konfiguracji aplikacji z wcześniejszych wersji programu ASP.NET, zobacz <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="61923-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="61923-1015">Konfiguracja domyślna</span><span class="sxs-lookup"><span data-stu-id="61923-1015">Default configuration</span></span>

<span data-ttu-id="61923-1016">Aplikacje sieci Web oparte na ASP.NET Coreniu [nowych szablonów dotnet](/dotnet/core/tools/dotnet-new) są wywoływane <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="61923-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="61923-1017">`CreateDefaultBuilder`zapewnia domyślną konfigurację dla aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="61923-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="61923-1018">Poniższe zasady dotyczą aplikacji korzystających z [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="61923-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="61923-1019">Aby uzyskać szczegółowe informacje na temat konfiguracji domyślnej w przypadku korzystania z [hosta ogólnego](xref:fundamentals/host/generic-host), zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="61923-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="61923-1020">Konfiguracja hosta jest poświadczona z:</span><span class="sxs-lookup"><span data-stu-id="61923-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="61923-1021">Zmienne środowiskowe poprzedzone znakiem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ) przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="61923-1022">Prefiks ( `ASPNETCORE_` ) jest usuwany, gdy są ładowane pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="61923-1023">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="61923-1024">Podano konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="61923-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="61923-1025">*appSettings. JSON* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="61923-1026">*appSettings. {Environment}. JSON* przy użyciu [dostawcy konfiguracji pliku](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="61923-1027">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="61923-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="61923-1028">Zmienne środowiskowe używające [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="61923-1029">Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="61923-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="61923-1030">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="61923-1030">Security</span></span>

<span data-ttu-id="61923-1031">Aby zabezpieczyć poufne dane konfiguracji, należy zastosować następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="61923-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="61923-1032">Nie należy przechowywać haseł ani innych danych poufnych w kodzie dostawcy konfiguracji ani w plikach konfiguracji zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="61923-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="61923-1033">Nie używaj tajemnic produkcyjnych w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="61923-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="61923-1034">Określ wpisy tajne poza projektem, aby nie mogły zostać przypadkowo przekazane do repozytorium kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="61923-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="61923-1035">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="61923-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="61923-1036"><xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="61923-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="61923-1037">Menedżer wpisów tajnych używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="61923-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="61923-1038">Dostawca konfiguracji plików został opisany w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="61923-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="61923-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpieczne przechowywanie wpisów tajnych aplikacji dla ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="61923-1040">Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="61923-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="61923-1041">Hierarchiczne dane konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="61923-1042">Interfejs API konfiguracji jest w stanie utrzymywać hierarchiczne dane konfiguracji przez spłaszczonie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="61923-1043">W poniższym pliku JSON cztery klucze istnieją w hierarchii strukturalnej dwóch sekcji:</span><span class="sxs-lookup"><span data-stu-id="61923-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="61923-1044">Gdy plik jest odczytywany do konfiguracji, są tworzone unikatowe klucze, aby zachować oryginalną hierarchiczną strukturę danych źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="61923-1045">Sekcje i klucze są spłaszczone przy użyciu dwukropka (), `:` Aby zachować oryginalną strukturę:</span><span class="sxs-lookup"><span data-stu-id="61923-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="61923-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="61923-1046">section0:key0</span></span>
* <span data-ttu-id="61923-1047">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-1047">section0:key1</span></span>
* <span data-ttu-id="61923-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="61923-1048">section1:key0</span></span>
* <span data-ttu-id="61923-1049">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-1049">section1:key1</span></span>

<span data-ttu-id="61923-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody i są dostępne do izolowania sekcji i elementów podrzędnych sekcji w danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="61923-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="61923-1051">Te metody są opisane w dalszej [części GetSection, GetChildren i EXISTS](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="61923-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="61923-1052">Konwencje</span><span class="sxs-lookup"><span data-stu-id="61923-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="61923-1053">Źródła i dostawcy</span><span class="sxs-lookup"><span data-stu-id="61923-1053">Sources and providers</span></span>

<span data-ttu-id="61923-1054">Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w jakiej są określone przez ich dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="61923-1055">Dostawcy konfiguracji implementujący funkcję wykrywania zmian mają możliwość ponownego załadowania konfiguracji, gdy ustawienie podstawowe zostanie zmienione.</span><span class="sxs-lookup"><span data-stu-id="61923-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="61923-1056">Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.</span><span class="sxs-lookup"><span data-stu-id="61923-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="61923-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration>jest dostępny w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="61923-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>można wstrzyknąć do Razor stron <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> lub MVC, <xref:Microsoft.AspNetCore.Mvc.Controller> Aby uzyskać konfigurację dla klasy.</span><span class="sxs-lookup"><span data-stu-id="61923-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="61923-1059">W poniższych przykładach `_config` pole jest używane w celu uzyskania dostępu do wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="61923-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="61923-1060">Dostawcy konfiguracji nie mogą używać DI, ponieważ są niedostępne, gdy są skonfigurowane przez hosta.</span><span class="sxs-lookup"><span data-stu-id="61923-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="61923-1061">Klucze</span><span class="sxs-lookup"><span data-stu-id="61923-1061">Keys</span></span>

<span data-ttu-id="61923-1062">Klucze konfiguracji przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="61923-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="61923-1063">W kluczach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="61923-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="61923-1064">Na przykład `ConnectionString` i `connectionstring` są traktowane jako równoważne klucze.</span><span class="sxs-lookup"><span data-stu-id="61923-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="61923-1065">Jeśli wartość tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, Ostatnia wartość ustawiona w tym kluczu jest używana.</span><span class="sxs-lookup"><span data-stu-id="61923-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="61923-1066">Klucze hierarchiczne</span><span class="sxs-lookup"><span data-stu-id="61923-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="61923-1067">W interfejsie API konfiguracji, separator dwukropek ( `:` ) działa na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="61923-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="61923-1068">W zmiennych środowiskowych separator dwukropek może nie zadziałał na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="61923-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="61923-1069">Podwójne podkreślenie ( `__` ) jest obsługiwane przez wszystkie platformy i automatycznie konwertowane na dwukropek.</span><span class="sxs-lookup"><span data-stu-id="61923-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="61923-1070">W Azure Key Vault klucze hierarchiczne używają `--` (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="61923-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="61923-1071">Napisz kod, aby zastąpić łączniki dwukropkiem, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="61923-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="61923-1073">Powiązanie tablicowe zostało opisane w sekcji [Powiązywanie tablicy z klasą](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="61923-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="61923-1074">Wartości</span><span class="sxs-lookup"><span data-stu-id="61923-1074">Values</span></span>

<span data-ttu-id="61923-1075">Wartości konfiguracyjne przyjmują następujące konwencje:</span><span class="sxs-lookup"><span data-stu-id="61923-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="61923-1076">Wartości są ciągami.</span><span class="sxs-lookup"><span data-stu-id="61923-1076">Values are strings.</span></span>
* <span data-ttu-id="61923-1077">Wartości null nie można przechowywać w konfiguracji ani powiązana z obiektami.</span><span class="sxs-lookup"><span data-stu-id="61923-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="61923-1078">Dostawcy</span><span class="sxs-lookup"><span data-stu-id="61923-1078">Providers</span></span>

<span data-ttu-id="61923-1079">W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="61923-1080">Dostawca</span><span class="sxs-lookup"><span data-stu-id="61923-1080">Provider</span></span> | <span data-ttu-id="61923-1081">Zapewnia konfigurację z&hellip;</span><span class="sxs-lookup"><span data-stu-id="61923-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="61923-1082">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1083">'Blazor'</span></span>
- <span data-ttu-id="61923-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1084">'Identity'</span></span>
- <span data-ttu-id="61923-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1086">'Razor'</span></span>
- <span data-ttu-id="61923-1087">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1088">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1089">'Blazor'</span></span>
- <span data-ttu-id="61923-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1090">'Identity'</span></span>
- <span data-ttu-id="61923-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1092">'Razor'</span></span>
- <span data-ttu-id="61923-1093">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1094">---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1095">'Blazor'</span></span>
- <span data-ttu-id="61923-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1096">'Identity'</span></span>
- <span data-ttu-id="61923-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1098">'Razor'</span></span>
- <span data-ttu-id="61923-1099">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1100">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1101">'Blazor'</span></span>
- <span data-ttu-id="61923-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1102">'Identity'</span></span>
- <span data-ttu-id="61923-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1104">'Razor'</span></span>
- <span data-ttu-id="61923-1105">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1106">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1107">'Blazor'</span></span>
- <span data-ttu-id="61923-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1108">'Identity'</span></span>
- <span data-ttu-id="61923-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1110">'Razor'</span></span>
- <span data-ttu-id="61923-1111">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1112">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1113">'Blazor'</span></span>
- <span data-ttu-id="61923-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1114">'Identity'</span></span>
- <span data-ttu-id="61923-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1116">'Razor'</span></span>
- <span data-ttu-id="61923-1117">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1118">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1119">'Blazor'</span></span>
- <span data-ttu-id="61923-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1120">'Identity'</span></span>
- <span data-ttu-id="61923-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1122">'Razor'</span></span>
- <span data-ttu-id="61923-1123">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1124">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1125">'Blazor'</span></span>
- <span data-ttu-id="61923-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1126">'Identity'</span></span>
- <span data-ttu-id="61923-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1128">'Razor'</span></span>
- <span data-ttu-id="61923-1129">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1130">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1131">'Blazor'</span></span>
- <span data-ttu-id="61923-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1132">'Identity'</span></span>
- <span data-ttu-id="61923-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1134">'Razor'</span></span>
- <span data-ttu-id="61923-1135">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1136">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1137">'Blazor'</span></span>
- <span data-ttu-id="61923-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1138">'Identity'</span></span>
- <span data-ttu-id="61923-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1140">'Razor'</span></span>
- <span data-ttu-id="61923-1141">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1142">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1143">'Blazor'</span></span>
- <span data-ttu-id="61923-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1144">'Identity'</span></span>
- <span data-ttu-id="61923-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1146">'Razor'</span></span>
- <span data-ttu-id="61923-1147">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1148">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1149">'Blazor'</span></span>
- <span data-ttu-id="61923-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1150">'Identity'</span></span>
- <span data-ttu-id="61923-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1152">'Razor'</span></span>
- <span data-ttu-id="61923-1153">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1154">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1155">'Blazor'</span></span>
- <span data-ttu-id="61923-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1156">'Identity'</span></span>
- <span data-ttu-id="61923-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1158">'Razor'</span></span>
- <span data-ttu-id="61923-1159">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1160">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1161">'Blazor'</span></span>
- <span data-ttu-id="61923-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1162">'Identity'</span></span>
- <span data-ttu-id="61923-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1164">'Razor'</span></span>
- <span data-ttu-id="61923-1165">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1166">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1167">'Blazor'</span></span>
- <span data-ttu-id="61923-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1168">'Identity'</span></span>
- <span data-ttu-id="61923-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1170">'Razor'</span></span>
- <span data-ttu-id="61923-1171">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1172">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1173">'Blazor'</span></span>
- <span data-ttu-id="61923-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1174">'Identity'</span></span>
- <span data-ttu-id="61923-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1176">'Razor'</span></span>
- <span data-ttu-id="61923-1177">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1178">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1179">'Blazor'</span></span>
- <span data-ttu-id="61923-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1180">'Identity'</span></span>
- <span data-ttu-id="61923-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1182">'Razor'</span></span>
- <span data-ttu-id="61923-1183">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1184">------------------ | | [Dostawca konfiguracji Azure Key Vault](xref:security/key-vault-configuration) (tematy dotyczące*zabezpieczeń* ) | Azure Key Vault | | [Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure) | Konfiguracja aplikacji platformy Azure | | [Dostawca konfiguracji wiersza polecenia](#command-line-configuration-provider) | Parametry wiersza polecenia | | [Niestandardowy dostawca konfiguracji](#custom-configuration-provider) | Źródło niestandardowe | | [Dostawca konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider) | Zmienne środowiskowe | | [Dostawca konfiguracji plików](#file-configuration-provider) | Pliki (INI, JSON, XML) | | [Dostawca konfiguracji klucza dla plików](#key-per-file-configuration-provider) | Pliki katalogu | | [Dostawca konfiguracji pamięci](#memory-configuration-provider) | Kolekcje w pamięci | Wpisy | [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tematy dotyczące*zabezpieczeń* ) | Plik w katalogu profilu użytkownika |</span><span class="sxs-lookup"><span data-stu-id="61923-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="61923-1185">Źródła konfiguracji są odczytywane w kolejności, w jakiej dostawcy konfiguracji są określeni podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="61923-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="61923-1186">Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w jakiej kod ich rozmieszcza.</span><span class="sxs-lookup"><span data-stu-id="61923-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="61923-1187">Zamów dostawców konfiguracji w kodzie, aby odpowiadały priorytetom źródłowych źródeł konfiguracji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="61923-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="61923-1188">Typową sekwencją dostawców konfiguracji jest:</span><span class="sxs-lookup"><span data-stu-id="61923-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="61923-1189">Pliki (*appSettings. JSON*, *appSettings. { Environment}. JSON*, gdzie `{Environment}` to bieżące środowisko hostingu aplikacji)</span><span class="sxs-lookup"><span data-stu-id="61923-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="61923-1190">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="61923-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="61923-1191">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) (tylko środowisko programistyczne)</span><span class="sxs-lookup"><span data-stu-id="61923-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="61923-1192">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="61923-1192">Environment variables</span></span>
1. <span data-ttu-id="61923-1193">Argumenty wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-1193">Command-line arguments</span></span>

<span data-ttu-id="61923-1194">Typowym celem jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatni w serii dostawców, aby zezwolić na argumenty wiersza polecenia, aby przesłonić konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="61923-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="61923-1195">Poprzednia sekwencja dostawców jest używana, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu programu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="61923-1196">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="61923-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="61923-1197">Konfigurowanie konstruktora hostów za pomocą UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="61923-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="61923-1198">Aby skonfigurować konstruktora hosta, należy wywołać <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konstruktora hosta z konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="61923-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="61923-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="61923-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="61923-1200">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić dostawców konfiguracji aplikacji oprócz tych dodanych automatycznie przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="61923-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="61923-1201">Zastąp poprzednią konfigurację argumentami wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="61923-1202">Aby podać konfigurację aplikacji, którą można zastąpić za pomocą argumentów wiersza polecenia, wywołaj `AddCommandLine` ostatni:</span><span class="sxs-lookup"><span data-stu-id="61923-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="61923-1203">Usuń dostawców dodanych przez CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="61923-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="61923-1204">Aby usunąć dostawców dodanych przez `CreateDefaultBuilder` , najpierw Wywołaj polecenie [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) w [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="61923-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="61923-1205">Użyj konfiguracji podczas uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="61923-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="61923-1206">Konfiguracja dostarczona do aplikacji w programie `ConfigureAppConfiguration` jest dostępna podczas uruchamiania aplikacji, w tym `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="61923-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="61923-1207">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja dostępu podczas uruchamiania](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="61923-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="61923-1208">Dostawca konfiguracji wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="61923-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="61923-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="61923-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="61923-1210">Aby uaktywnić konfigurację wiersza polecenia, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda rozszerzenia jest wywoływana w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="61923-1211">`AddCommandLine`jest wywoływana automatycznie, gdy `CreateDefaultBuilder(string [])` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="61923-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="61923-1212">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="61923-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="61923-1213">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="61923-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="61923-1214">Opcjonalna konfiguracja z pliku *appSettings. JSON* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="61923-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="61923-1215">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="61923-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="61923-1216">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="61923-1216">Environment variables.</span></span>

<span data-ttu-id="61923-1217">`CreateDefaultBuilder`dodaje dostawcę konfiguracji wiersza polecenia Last.</span><span class="sxs-lookup"><span data-stu-id="61923-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="61923-1218">Argumenty wiersza polecenia przekazane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="61923-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="61923-1219">`CreateDefaultBuilder`działa, gdy host jest skonstruowany.</span><span class="sxs-lookup"><span data-stu-id="61923-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="61923-1220">W związku z tym konfiguracja wiersza polecenia aktywowana przez program `CreateDefaultBuilder` może mieć wpływ na sposób konfigurowania hosta.</span><span class="sxs-lookup"><span data-stu-id="61923-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="61923-1221">W przypadku aplikacji opartych na ASP.NET Core szablonach program `AddCommandLine` został już wywołany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="61923-1222">Aby dodać kolejnych dostawców konfiguracji i zachować możliwość przesłonięcia konfiguracji od tych dostawców za pomocą argumentów wiersza polecenia, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj jako `AddCommandLine` ostatni.</span><span class="sxs-lookup"><span data-stu-id="61923-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="61923-1223">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="61923-1223">**Example**</span></span>

<span data-ttu-id="61923-1224">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="61923-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="61923-1225">Otwórz wiersz polecenia w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="61923-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="61923-1226">Podaj do polecenia argument wiersza polecenia `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="61923-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="61923-1227">Po uruchomieniu aplikacji otwórz w przeglądarce aplikację w lokalizacji `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="61923-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="61923-1228">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla argumentu wiersza polecenia konfiguracji dostarczonego do `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="61923-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="61923-1229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="61923-1229">Arguments</span></span>

<span data-ttu-id="61923-1230">Wartość musi następować po znaku równości ( `=` ) lub klucz musi mieć prefiks ( `--` lub `/` ), gdy wartość znajduje się w miejscu.</span><span class="sxs-lookup"><span data-stu-id="61923-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="61923-1231">Wartość nie jest wymagana, jeśli jest używany znak równości (na przykład `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="61923-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="61923-1232">Prefiks klucza</span><span class="sxs-lookup"><span data-stu-id="61923-1232">Key prefix</span></span>               | <span data-ttu-id="61923-1233">Przykład</span><span class="sxs-lookup"><span data-stu-id="61923-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="61923-1234">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1235">'Blazor'</span></span>
- <span data-ttu-id="61923-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1236">'Identity'</span></span>
- <span data-ttu-id="61923-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1238">'Razor'</span></span>
- <span data-ttu-id="61923-1239">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1240">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1241">'Blazor'</span></span>
- <span data-ttu-id="61923-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1242">'Identity'</span></span>
- <span data-ttu-id="61923-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1244">'Razor'</span></span>
- <span data-ttu-id="61923-1245">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1246">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1247">'Blazor'</span></span>
- <span data-ttu-id="61923-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1248">'Identity'</span></span>
- <span data-ttu-id="61923-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1250">'Razor'</span></span>
- <span data-ttu-id="61923-1251">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1252">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1253">'Blazor'</span></span>
- <span data-ttu-id="61923-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1254">'Identity'</span></span>
- <span data-ttu-id="61923-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1256">'Razor'</span></span>
- <span data-ttu-id="61923-1257">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1258">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1259">'Blazor'</span></span>
- <span data-ttu-id="61923-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1260">'Identity'</span></span>
- <span data-ttu-id="61923-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1262">'Razor'</span></span>
- <span data-ttu-id="61923-1263">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1264">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1265">'Blazor'</span></span>
- <span data-ttu-id="61923-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1266">'Identity'</span></span>
- <span data-ttu-id="61923-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1268">'Razor'</span></span>
- <span data-ttu-id="61923-1269">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1270">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1271">'Blazor'</span></span>
- <span data-ttu-id="61923-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1272">'Identity'</span></span>
- <span data-ttu-id="61923-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1274">'Razor'</span></span>
- <span data-ttu-id="61923-1275">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1276">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1277">'Blazor'</span></span>
- <span data-ttu-id="61923-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1278">'Identity'</span></span>
- <span data-ttu-id="61923-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1280">'Razor'</span></span>
- <span data-ttu-id="61923-1281">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1282">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1283">'Blazor'</span></span>
- <span data-ttu-id="61923-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1284">'Identity'</span></span>
- <span data-ttu-id="61923-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1286">'Razor'</span></span>
- <span data-ttu-id="61923-1287">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1288">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1289">'Blazor'</span></span>
- <span data-ttu-id="61923-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1290">'Identity'</span></span>
- <span data-ttu-id="61923-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1292">'Razor'</span></span>
- <span data-ttu-id="61923-1293">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1294">------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1295">'Blazor'</span></span>
- <span data-ttu-id="61923-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1296">'Identity'</span></span>
- <span data-ttu-id="61923-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1298">'Razor'</span></span>
- <span data-ttu-id="61923-1299">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1300">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1301">'Blazor'</span></span>
- <span data-ttu-id="61923-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1302">'Identity'</span></span>
- <span data-ttu-id="61923-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1304">'Razor'</span></span>
- <span data-ttu-id="61923-1305">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1306">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1307">'Blazor'</span></span>
- <span data-ttu-id="61923-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1308">'Identity'</span></span>
- <span data-ttu-id="61923-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1310">'Razor'</span></span>
- <span data-ttu-id="61923-1311">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1312">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1313">'Blazor'</span></span>
- <span data-ttu-id="61923-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1314">'Identity'</span></span>
- <span data-ttu-id="61923-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1316">'Razor'</span></span>
- <span data-ttu-id="61923-1317">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1318">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1319">'Blazor'</span></span>
- <span data-ttu-id="61923-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1320">'Identity'</span></span>
- <span data-ttu-id="61923-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1322">'Razor'</span></span>
- <span data-ttu-id="61923-1323">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1324">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1325">'Blazor'</span></span>
- <span data-ttu-id="61923-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1326">'Identity'</span></span>
- <span data-ttu-id="61923-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1328">'Razor'</span></span>
- <span data-ttu-id="61923-1329">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1330">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1331">'Blazor'</span></span>
- <span data-ttu-id="61923-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1332">'Identity'</span></span>
- <span data-ttu-id="61923-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1334">'Razor'</span></span>
- <span data-ttu-id="61923-1335">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1336">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1337">'Blazor'</span></span>
- <span data-ttu-id="61923-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1338">'Identity'</span></span>
- <span data-ttu-id="61923-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1340">'Razor'</span></span>
- <span data-ttu-id="61923-1341">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1342">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1343">'Blazor'</span></span>
- <span data-ttu-id="61923-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1344">'Identity'</span></span>
- <span data-ttu-id="61923-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1346">'Razor'</span></span>
- <span data-ttu-id="61923-1347">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1348">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1349">'Blazor'</span></span>
- <span data-ttu-id="61923-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1350">'Identity'</span></span>
- <span data-ttu-id="61923-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1352">'Razor'</span></span>
- <span data-ttu-id="61923-1353">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1354">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1355">'Blazor'</span></span>
- <span data-ttu-id="61923-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1356">'Identity'</span></span>
- <span data-ttu-id="61923-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1358">'Razor'</span></span>
- <span data-ttu-id="61923-1359">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1360">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1361">'Blazor'</span></span>
- <span data-ttu-id="61923-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1362">'Identity'</span></span>
- <span data-ttu-id="61923-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1364">'Razor'</span></span>
- <span data-ttu-id="61923-1365">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1366">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1367">'Blazor'</span></span>
- <span data-ttu-id="61923-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1368">'Identity'</span></span>
- <span data-ttu-id="61923-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1370">'Razor'</span></span>
- <span data-ttu-id="61923-1371">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1372">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1373">'Blazor'</span></span>
- <span data-ttu-id="61923-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1374">'Identity'</span></span>
- <span data-ttu-id="61923-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1376">'Razor'</span></span>
- <span data-ttu-id="61923-1377">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1378">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1379">'Blazor'</span></span>
- <span data-ttu-id="61923-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1380">'Identity'</span></span>
- <span data-ttu-id="61923-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1382">'Razor'</span></span>
- <span data-ttu-id="61923-1383">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1384">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1385">'Blazor'</span></span>
- <span data-ttu-id="61923-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1386">'Identity'</span></span>
- <span data-ttu-id="61923-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1388">'Razor'</span></span>
- <span data-ttu-id="61923-1389">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1390">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1391">'Blazor'</span></span>
- <span data-ttu-id="61923-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1392">'Identity'</span></span>
- <span data-ttu-id="61923-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1394">'Razor'</span></span>
- <span data-ttu-id="61923-1395">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1396">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1397">'Blazor'</span></span>
- <span data-ttu-id="61923-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1398">'Identity'</span></span>
- <span data-ttu-id="61923-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1400">'Razor'</span></span>
- <span data-ttu-id="61923-1401">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1402">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1403">'Blazor'</span></span>
- <span data-ttu-id="61923-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1404">'Identity'</span></span>
- <span data-ttu-id="61923-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1406">'Razor'</span></span>
- <span data-ttu-id="61923-1407">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1408">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1409">'Blazor'</span></span>
- <span data-ttu-id="61923-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1410">'Identity'</span></span>
- <span data-ttu-id="61923-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1412">'Razor'</span></span>
- <span data-ttu-id="61923-1413">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1414">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1415">'Blazor'</span></span>
- <span data-ttu-id="61923-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1416">'Identity'</span></span>
- <span data-ttu-id="61923-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1418">'Razor'</span></span>
- <span data-ttu-id="61923-1419">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1420">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1421">'Blazor'</span></span>
- <span data-ttu-id="61923-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1422">'Identity'</span></span>
- <span data-ttu-id="61923-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1424">'Razor'</span></span>
- <span data-ttu-id="61923-1425">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1426">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1427">'Blazor'</span></span>
- <span data-ttu-id="61923-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1428">'Identity'</span></span>
- <span data-ttu-id="61923-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1430">'Razor'</span></span>
- <span data-ttu-id="61923-1431">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1432">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1433">'Blazor'</span></span>
- <span data-ttu-id="61923-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1434">'Identity'</span></span>
- <span data-ttu-id="61923-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1436">'Razor'</span></span>
- <span data-ttu-id="61923-1437">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1438">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1439">'Blazor'</span></span>
- <span data-ttu-id="61923-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1440">'Identity'</span></span>
- <span data-ttu-id="61923-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1442">'Razor'</span></span>
- <span data-ttu-id="61923-1443">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1444">--------------------------- | | Brak prefiksu | `CommandLineKey1=value1`                               |
| Dwie kreski ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Ukośnik ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="61923-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="61923-1445">W tym samym poleceniu nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="61923-1446">Przykładowe polecenia:</span><span class="sxs-lookup"><span data-stu-id="61923-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="61923-1447">Mapowanie przełączników</span><span class="sxs-lookup"><span data-stu-id="61923-1447">Switch mappings</span></span>

<span data-ttu-id="61923-1448">Mapowania przełączników Zezwalaj na logikę zamiany nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="61923-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="61923-1449">Podczas ręcznego kompilowania konfiguracji za pomocą programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> należy udostępnić słownik przemieszczeń Switch do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="61923-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="61923-1450">Gdy jest używany słownik mapowania przełączników, słownik jest sprawdzany dla klucza, który pasuje do klucza dostarczonego przez argument wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="61923-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="61923-1451">Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (wymiana klucza) zostanie przeniesiona z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="61923-1452">Mapowanie przełącznika jest wymagane dla każdego klucza wiersza polecenia poprzedzonego pojedynczą kreską ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="61923-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="61923-1453">Przełącz reguły klucza słownika mapowania:</span><span class="sxs-lookup"><span data-stu-id="61923-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="61923-1454">Przełączniki muszą zaczynać się kreską ( `-` ) lub podwójną kreską ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="61923-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="61923-1455">Słownik mapowania przełącznika nie może zawierać zduplikowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="61923-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="61923-1456">Utwórz słownik mapowań mapowania.</span><span class="sxs-lookup"><span data-stu-id="61923-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="61923-1457">W poniższym przykładzie są tworzone dwa mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="61923-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="61923-1458">Po skompilowaniu hosta Wywołaj `AddCommandLine` przy użyciu słownika mapowania przełączników:</span><span class="sxs-lookup"><span data-stu-id="61923-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="61923-1459">W przypadku aplikacji korzystających z mapowań przełączników wywołanie nie `CreateDefaultBuilder` powinno przekazywać argumentów.</span><span class="sxs-lookup"><span data-stu-id="61923-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="61923-1460">`CreateDefaultBuilder` `AddCommandLine` Wywołanie metody nie obejmuje zamapowanych przełączników i nie ma sposobu przekazywania słownika mapowania przełącznika do `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="61923-1461">Rozwiązanie nie przekazuje argumentów do, `CreateDefaultBuilder` ale zamiast tego zezwala metodzie `ConfigurationBuilder` metody `AddCommandLine` na przetwarzanie zarówno argumentów, jak i słownika mapowania przełącznika.</span><span class="sxs-lookup"><span data-stu-id="61923-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="61923-1462">Po utworzeniu słownika mapowań przełączników zawiera dane przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="61923-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="61923-1463">Klucz</span><span class="sxs-lookup"><span data-stu-id="61923-1463">Key</span></span>       | <span data-ttu-id="61923-1464">Wartość</span><span class="sxs-lookup"><span data-stu-id="61923-1464">Value</span></span>             |
| ---
<span data-ttu-id="61923-1465">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1466">'Blazor'</span></span>
- <span data-ttu-id="61923-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1467">'Identity'</span></span>
- <span data-ttu-id="61923-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1469">'Razor'</span></span>
- <span data-ttu-id="61923-1470">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1471">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1472">'Blazor'</span></span>
- <span data-ttu-id="61923-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1473">'Identity'</span></span>
- <span data-ttu-id="61923-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1475">'Razor'</span></span>
- <span data-ttu-id="61923-1476">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1477">----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1478">'Blazor'</span></span>
- <span data-ttu-id="61923-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1479">'Identity'</span></span>
- <span data-ttu-id="61923-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1481">'Razor'</span></span>
- <span data-ttu-id="61923-1482">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1483">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1484">'Blazor'</span></span>
- <span data-ttu-id="61923-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1485">'Identity'</span></span>
- <span data-ttu-id="61923-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1487">'Razor'</span></span>
- <span data-ttu-id="61923-1488">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1489">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1490">'Blazor'</span></span>
- <span data-ttu-id="61923-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1491">'Identity'</span></span>
- <span data-ttu-id="61923-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1493">'Razor'</span></span>
- <span data-ttu-id="61923-1494">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1495">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1496">'Blazor'</span></span>
- <span data-ttu-id="61923-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1497">'Identity'</span></span>
- <span data-ttu-id="61923-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1499">'Razor'</span></span>
- <span data-ttu-id="61923-1500">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1501">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1502">'Blazor'</span></span>
- <span data-ttu-id="61923-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1503">'Identity'</span></span>
- <span data-ttu-id="61923-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1505">'Razor'</span></span>
- <span data-ttu-id="61923-1506">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1507">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1508">'Blazor'</span></span>
- <span data-ttu-id="61923-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1509">'Identity'</span></span>
- <span data-ttu-id="61923-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1511">'Razor'</span></span>
- <span data-ttu-id="61923-1512">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="61923-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="61923-1514">Jeśli klucze mapowane przez przełącznik są używane podczas uruchamiania aplikacji, konfiguracja otrzymuje wartość konfiguracji klucza dostarczonego przez słownik:</span><span class="sxs-lookup"><span data-stu-id="61923-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="61923-1515">Po uruchomieniu poprzedniego polecenia Konfiguracja zawiera wartości pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="61923-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="61923-1516">Klucz</span><span class="sxs-lookup"><span data-stu-id="61923-1516">Key</span></span>               | <span data-ttu-id="61923-1517">Wartość</span><span class="sxs-lookup"><span data-stu-id="61923-1517">Value</span></span>    |
| ---
<span data-ttu-id="61923-1518">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1519">'Blazor'</span></span>
- <span data-ttu-id="61923-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1520">'Identity'</span></span>
- <span data-ttu-id="61923-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1522">'Razor'</span></span>
- <span data-ttu-id="61923-1523">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1524">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1525">'Blazor'</span></span>
- <span data-ttu-id="61923-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1526">'Identity'</span></span>
- <span data-ttu-id="61923-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1528">'Razor'</span></span>
- <span data-ttu-id="61923-1529">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1530">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1531">'Blazor'</span></span>
- <span data-ttu-id="61923-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1532">'Identity'</span></span>
- <span data-ttu-id="61923-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1534">'Razor'</span></span>
- <span data-ttu-id="61923-1535">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1536">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1537">'Blazor'</span></span>
- <span data-ttu-id="61923-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1538">'Identity'</span></span>
- <span data-ttu-id="61923-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1540">'Razor'</span></span>
- <span data-ttu-id="61923-1541">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1542">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1543">'Blazor'</span></span>
- <span data-ttu-id="61923-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1544">'Identity'</span></span>
- <span data-ttu-id="61923-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1546">'Razor'</span></span>
- <span data-ttu-id="61923-1547">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1548">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1549">'Blazor'</span></span>
- <span data-ttu-id="61923-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1550">'Identity'</span></span>
- <span data-ttu-id="61923-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1552">'Razor'</span></span>
- <span data-ttu-id="61923-1553">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1554">--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1555">'Blazor'</span></span>
- <span data-ttu-id="61923-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1556">'Identity'</span></span>
- <span data-ttu-id="61923-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1558">'Razor'</span></span>
- <span data-ttu-id="61923-1559">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1560">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1561">'Blazor'</span></span>
- <span data-ttu-id="61923-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1562">'Identity'</span></span>
- <span data-ttu-id="61923-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1564">'Razor'</span></span>
- <span data-ttu-id="61923-1565">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="61923-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="61923-1567">Dostawca konfiguracji zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="61923-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="61923-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Ładowanie konfiguracji ze zmiennej środowiskowej par klucz-wartość w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="61923-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="61923-1569">Aby uaktywnić konfigurację zmiennych środowiskowych, wywołaj <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="61923-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia ustawianie zmiennych środowiskowych w witrynie Azure Portal, które mogą przesłonić konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="61923-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="61923-1571">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="61923-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="61923-1572">`AddEnvironmentVariables`służy do ładowania zmiennych środowiskowych, które są poprzedzone `ASPNETCORE_` [konfiguracją hosta](#host-versus-app-configuration) , gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="61923-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="61923-1573">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="61923-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="61923-1574">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="61923-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="61923-1575">Konfiguracja aplikacji z nieoznaczonych zmiennych środowiskowych przez wywołanie `AddEnvironmentVariables` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="61923-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="61923-1576">Opcjonalna konfiguracja z pliku *appSettings. JSON* i *appSettings. { Environment}. JSON* — pliki.</span><span class="sxs-lookup"><span data-stu-id="61923-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="61923-1577">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="61923-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="61923-1578">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="61923-1578">Command-line arguments.</span></span>

<span data-ttu-id="61923-1579">Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z poziomu kluczy tajnych użytkownika i plików *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="61923-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="61923-1580">Wywołanie dostawcy w tym miejscu pozwala odczytywać zmienne środowiskowe w czasie wykonywania w celu przesłania konfiguracji ustawionych przez klucze tajne użytkownika i pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="61923-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="61923-1581">Aby zapewnić konfigurację aplikacji na podstawie dodatkowych zmiennych środowiskowych, wywołaj dodatkowych dostawców aplikacji w `ConfigureAppConfiguration` i Wywołaj `AddEnvironmentVariables` z prefiksem:</span><span class="sxs-lookup"><span data-stu-id="61923-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="61923-1582">Wywołaj `AddEnvironmentVariables` ostatni, aby zezwolić na zmienne środowiskowe z danym prefiksem, aby przesłonić wartości od innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="61923-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="61923-1583">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="61923-1583">**Example**</span></span>

<span data-ttu-id="61923-1584">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje wywołanie `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="61923-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="61923-1585">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="61923-1585">Run the sample app.</span></span> <span data-ttu-id="61923-1586">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="61923-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="61923-1587">Zwróć uwagę, że dane wyjściowe zawierają parę klucz-wartość dla zmiennej środowiskowej `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="61923-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="61923-1588">Wartość odzwierciedla środowisko, w którym jest uruchomiona aplikacja, zazwyczaj w `Development` przypadku uruchamiania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="61923-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="61923-1589">Aby zachować listę zmiennych środowiskowych renderowanych przez aplikację, aplikacja filtruje zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="61923-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="61923-1590">Zapoznaj się z plikiem przykładowej *strony aplikacji/index. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="61923-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="61923-1591">Aby uwidocznić wszystkie zmienne środowiskowe dostępne dla aplikacji, należy zmienić `FilteredConfiguration` stronę na *stronie/index. cshtml. cs* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="61923-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="61923-1592">Prefiksy</span><span class="sxs-lookup"><span data-stu-id="61923-1592">Prefixes</span></span>

<span data-ttu-id="61923-1593">Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane podczas dostarczania prefiksu do `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="61923-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="61923-1594">Na przykład aby filtrować zmienne środowiskowe na prefiksie `CUSTOM_` , podaj prefiks dla dostawcy konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="61923-1595">Prefiks jest usuwany podczas tworzenia par klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="61923-1596">Podczas tworzenia konstruktora hostów Konfiguracja hosta jest zapewniana przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="61923-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="61923-1597">Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="61923-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="61923-1598">**Prefiksy parametrów połączenia**</span><span class="sxs-lookup"><span data-stu-id="61923-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="61923-1599">Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych parametrów połączenia związanych z konfigurowaniem parametrów połączenia platformy Azure dla środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="61923-1600">Zmienne środowiskowe z prefiksami podanymi w tabeli są ładowane do aplikacji, jeśli nie podano prefiksu `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="61923-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="61923-1601">Prefiks parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="61923-1601">Connection string prefix</span></span> | <span data-ttu-id="61923-1602">Dostawca</span><span class="sxs-lookup"><span data-stu-id="61923-1602">Provider</span></span> |
| ---
<span data-ttu-id="61923-1603">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1604">'Blazor'</span></span>
- <span data-ttu-id="61923-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1605">'Identity'</span></span>
- <span data-ttu-id="61923-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1607">'Razor'</span></span>
- <span data-ttu-id="61923-1608">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1609">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1610">'Blazor'</span></span>
- <span data-ttu-id="61923-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1611">'Identity'</span></span>
- <span data-ttu-id="61923-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1613">'Razor'</span></span>
- <span data-ttu-id="61923-1614">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1615">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1616">'Blazor'</span></span>
- <span data-ttu-id="61923-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1617">'Identity'</span></span>
- <span data-ttu-id="61923-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1619">'Razor'</span></span>
- <span data-ttu-id="61923-1620">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1621">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1622">'Blazor'</span></span>
- <span data-ttu-id="61923-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1623">'Identity'</span></span>
- <span data-ttu-id="61923-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1625">'Razor'</span></span>
- <span data-ttu-id="61923-1626">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1627">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1628">'Blazor'</span></span>
- <span data-ttu-id="61923-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1629">'Identity'</span></span>
- <span data-ttu-id="61923-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1631">'Razor'</span></span>
- <span data-ttu-id="61923-1632">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1633">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1634">'Blazor'</span></span>
- <span data-ttu-id="61923-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1635">'Identity'</span></span>
- <span data-ttu-id="61923-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1637">'Razor'</span></span>
- <span data-ttu-id="61923-1638">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1639">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1640">'Blazor'</span></span>
- <span data-ttu-id="61923-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1641">'Identity'</span></span>
- <span data-ttu-id="61923-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1643">'Razor'</span></span>
- <span data-ttu-id="61923-1644">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1645">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1646">'Blazor'</span></span>
- <span data-ttu-id="61923-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1647">'Identity'</span></span>
- <span data-ttu-id="61923-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1649">'Razor'</span></span>
- <span data-ttu-id="61923-1650">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1651">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1652">'Blazor'</span></span>
- <span data-ttu-id="61923-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1653">'Identity'</span></span>
- <span data-ttu-id="61923-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1655">'Razor'</span></span>
- <span data-ttu-id="61923-1656">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1657">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1658">'Blazor'</span></span>
- <span data-ttu-id="61923-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1659">'Identity'</span></span>
- <span data-ttu-id="61923-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1661">'Razor'</span></span>
- <span data-ttu-id="61923-1662">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1663">------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1664">'Blazor'</span></span>
- <span data-ttu-id="61923-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1665">'Identity'</span></span>
- <span data-ttu-id="61923-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1667">'Razor'</span></span>
- <span data-ttu-id="61923-1668">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1669">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1670">'Blazor'</span></span>
- <span data-ttu-id="61923-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1671">'Identity'</span></span>
- <span data-ttu-id="61923-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1673">'Razor'</span></span>
- <span data-ttu-id="61923-1674">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1675">---- | | `CUSTOMCONNSTR_` | Dostawca niestandardowy | | `MYSQLCONNSTR_` | [Baza danych MySQL](https://www.mysql.com/) |
 | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="61923-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="61923-1676">Gdy zmienna środowiskowa zostanie odnaleziona i załadowana do konfiguracji z dowolnymi z czterech prefiksów pokazanych w tabeli:</span><span class="sxs-lookup"><span data-stu-id="61923-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="61923-1677">Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej środowiskowej i dodanie sekcji klucza konfiguracji ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="61923-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="61923-1678">Zostanie utworzona nowa para klucz-wartość konfiguracji, która reprezentuje dostawcę połączenia bazy danych (z wyjątkiem tego `CUSTOMCONNSTR_` , który nie ma określonego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="61923-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="61923-1679">Klucz zmiennej środowiskowej</span><span class="sxs-lookup"><span data-stu-id="61923-1679">Environment variable key</span></span> | <span data-ttu-id="61923-1680">Przekonwertowany klucz konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-1680">Converted configuration key</span></span> | <span data-ttu-id="61923-1681">Wpis konfiguracji dostawcy</span><span class="sxs-lookup"><span data-stu-id="61923-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="61923-1682">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1683">'Blazor'</span></span>
- <span data-ttu-id="61923-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1684">'Identity'</span></span>
- <span data-ttu-id="61923-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1686">'Razor'</span></span>
- <span data-ttu-id="61923-1687">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1688">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1689">'Blazor'</span></span>
- <span data-ttu-id="61923-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1690">'Identity'</span></span>
- <span data-ttu-id="61923-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1692">'Razor'</span></span>
- <span data-ttu-id="61923-1693">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1694">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1695">'Blazor'</span></span>
- <span data-ttu-id="61923-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1696">'Identity'</span></span>
- <span data-ttu-id="61923-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1698">'Razor'</span></span>
- <span data-ttu-id="61923-1699">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1700">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1701">'Blazor'</span></span>
- <span data-ttu-id="61923-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1702">'Identity'</span></span>
- <span data-ttu-id="61923-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1704">'Razor'</span></span>
- <span data-ttu-id="61923-1705">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1706">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1707">'Blazor'</span></span>
- <span data-ttu-id="61923-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1708">'Identity'</span></span>
- <span data-ttu-id="61923-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1710">'Razor'</span></span>
- <span data-ttu-id="61923-1711">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1712">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1713">'Blazor'</span></span>
- <span data-ttu-id="61923-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1714">'Identity'</span></span>
- <span data-ttu-id="61923-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1716">'Razor'</span></span>
- <span data-ttu-id="61923-1717">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1718">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1719">'Blazor'</span></span>
- <span data-ttu-id="61923-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1720">'Identity'</span></span>
- <span data-ttu-id="61923-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1722">'Razor'</span></span>
- <span data-ttu-id="61923-1723">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1724">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1725">'Blazor'</span></span>
- <span data-ttu-id="61923-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1726">'Identity'</span></span>
- <span data-ttu-id="61923-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1728">'Razor'</span></span>
- <span data-ttu-id="61923-1729">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1730">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1731">'Blazor'</span></span>
- <span data-ttu-id="61923-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1732">'Identity'</span></span>
- <span data-ttu-id="61923-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1734">'Razor'</span></span>
- <span data-ttu-id="61923-1735">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1736">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1737">'Blazor'</span></span>
- <span data-ttu-id="61923-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1738">'Identity'</span></span>
- <span data-ttu-id="61923-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1740">'Razor'</span></span>
- <span data-ttu-id="61923-1741">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1742">------------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1743">'Blazor'</span></span>
- <span data-ttu-id="61923-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1744">'Identity'</span></span>
- <span data-ttu-id="61923-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1746">'Razor'</span></span>
- <span data-ttu-id="61923-1747">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1748">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1749">'Blazor'</span></span>
- <span data-ttu-id="61923-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1750">'Identity'</span></span>
- <span data-ttu-id="61923-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1752">'Razor'</span></span>
- <span data-ttu-id="61923-1753">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1754">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1755">'Blazor'</span></span>
- <span data-ttu-id="61923-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1756">'Identity'</span></span>
- <span data-ttu-id="61923-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1758">'Razor'</span></span>
- <span data-ttu-id="61923-1759">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1760">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1761">'Blazor'</span></span>
- <span data-ttu-id="61923-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1762">'Identity'</span></span>
- <span data-ttu-id="61923-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1764">'Razor'</span></span>
- <span data-ttu-id="61923-1765">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1766">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1767">'Blazor'</span></span>
- <span data-ttu-id="61923-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1768">'Identity'</span></span>
- <span data-ttu-id="61923-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1770">'Razor'</span></span>
- <span data-ttu-id="61923-1771">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1772">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1773">'Blazor'</span></span>
- <span data-ttu-id="61923-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1774">'Identity'</span></span>
- <span data-ttu-id="61923-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1776">'Razor'</span></span>
- <span data-ttu-id="61923-1777">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1778">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1779">'Blazor'</span></span>
- <span data-ttu-id="61923-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1780">'Identity'</span></span>
- <span data-ttu-id="61923-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1782">'Razor'</span></span>
- <span data-ttu-id="61923-1783">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1784">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1785">'Blazor'</span></span>
- <span data-ttu-id="61923-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1786">'Identity'</span></span>
- <span data-ttu-id="61923-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1788">'Razor'</span></span>
- <span data-ttu-id="61923-1789">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1790">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1791">'Blazor'</span></span>
- <span data-ttu-id="61923-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1792">'Identity'</span></span>
- <span data-ttu-id="61923-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1794">'Razor'</span></span>
- <span data-ttu-id="61923-1795">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1796">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1797">'Blazor'</span></span>
- <span data-ttu-id="61923-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1798">'Identity'</span></span>
- <span data-ttu-id="61923-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1800">'Razor'</span></span>
- <span data-ttu-id="61923-1801">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1802">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1803">'Blazor'</span></span>
- <span data-ttu-id="61923-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1804">'Identity'</span></span>
- <span data-ttu-id="61923-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1806">'Razor'</span></span>
- <span data-ttu-id="61923-1807">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-1808">-------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1809">'Blazor'</span></span>
- <span data-ttu-id="61923-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1810">'Identity'</span></span>
- <span data-ttu-id="61923-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1812">'Razor'</span></span>
- <span data-ttu-id="61923-1813">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1814">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1815">'Blazor'</span></span>
- <span data-ttu-id="61923-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1816">'Identity'</span></span>
- <span data-ttu-id="61923-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1818">'Razor'</span></span>
- <span data-ttu-id="61923-1819">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1820">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1821">'Blazor'</span></span>
- <span data-ttu-id="61923-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1822">'Identity'</span></span>
- <span data-ttu-id="61923-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1824">'Razor'</span></span>
- <span data-ttu-id="61923-1825">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1826">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1827">'Blazor'</span></span>
- <span data-ttu-id="61923-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1828">'Identity'</span></span>
- <span data-ttu-id="61923-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1830">'Razor'</span></span>
- <span data-ttu-id="61923-1831">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1832">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1833">'Blazor'</span></span>
- <span data-ttu-id="61923-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1834">'Identity'</span></span>
- <span data-ttu-id="61923-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1836">'Razor'</span></span>
- <span data-ttu-id="61923-1837">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1838">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1839">'Blazor'</span></span>
- <span data-ttu-id="61923-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1840">'Identity'</span></span>
- <span data-ttu-id="61923-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1842">'Razor'</span></span>
- <span data-ttu-id="61923-1843">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1844">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1845">'Blazor'</span></span>
- <span data-ttu-id="61923-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1846">'Identity'</span></span>
- <span data-ttu-id="61923-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1848">'Razor'</span></span>
- <span data-ttu-id="61923-1849">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1850">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1851">'Blazor'</span></span>
- <span data-ttu-id="61923-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1852">'Identity'</span></span>
- <span data-ttu-id="61923-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1854">'Razor'</span></span>
- <span data-ttu-id="61923-1855">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1856">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1857">'Blazor'</span></span>
- <span data-ttu-id="61923-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1858">'Identity'</span></span>
- <span data-ttu-id="61923-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1860">'Razor'</span></span>
- <span data-ttu-id="61923-1861">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1862">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1863">'Blazor'</span></span>
- <span data-ttu-id="61923-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1864">'Identity'</span></span>
- <span data-ttu-id="61923-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1866">'Razor'</span></span>
- <span data-ttu-id="61923-1867">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1868">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1869">'Blazor'</span></span>
- <span data-ttu-id="61923-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1870">'Identity'</span></span>
- <span data-ttu-id="61923-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1872">'Razor'</span></span>
- <span data-ttu-id="61923-1873">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1874">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1875">'Blazor'</span></span>
- <span data-ttu-id="61923-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1876">'Identity'</span></span>
- <span data-ttu-id="61923-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1878">'Razor'</span></span>
- <span data-ttu-id="61923-1879">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1880">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1881">'Blazor'</span></span>
- <span data-ttu-id="61923-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1882">'Identity'</span></span>
- <span data-ttu-id="61923-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1884">'Razor'</span></span>
- <span data-ttu-id="61923-1885">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1886">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1887">'Blazor'</span></span>
- <span data-ttu-id="61923-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1888">'Identity'</span></span>
- <span data-ttu-id="61923-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1890">'Razor'</span></span>
- <span data-ttu-id="61923-1891">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1892">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1893">'Blazor'</span></span>
- <span data-ttu-id="61923-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1894">'Identity'</span></span>
- <span data-ttu-id="61923-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1896">'Razor'</span></span>
- <span data-ttu-id="61923-1897">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1898">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1899">'Blazor'</span></span>
- <span data-ttu-id="61923-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1900">'Identity'</span></span>
- <span data-ttu-id="61923-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1902">'Razor'</span></span>
- <span data-ttu-id="61923-1903">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1904">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1905">'Blazor'</span></span>
- <span data-ttu-id="61923-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1906">'Identity'</span></span>
- <span data-ttu-id="61923-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1908">'Razor'</span></span>
- <span data-ttu-id="61923-1909">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1910">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1911">'Blazor'</span></span>
- <span data-ttu-id="61923-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1912">'Identity'</span></span>
- <span data-ttu-id="61923-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1914">'Razor'</span></span>
- <span data-ttu-id="61923-1915">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1916">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1917">'Blazor'</span></span>
- <span data-ttu-id="61923-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1918">'Identity'</span></span>
- <span data-ttu-id="61923-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1920">'Razor'</span></span>
- <span data-ttu-id="61923-1921">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1922">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1923">'Blazor'</span></span>
- <span data-ttu-id="61923-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1924">'Identity'</span></span>
- <span data-ttu-id="61923-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1926">'Razor'</span></span>
- <span data-ttu-id="61923-1927">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1928">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1929">'Blazor'</span></span>
- <span data-ttu-id="61923-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1930">'Identity'</span></span>
- <span data-ttu-id="61923-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1932">'Razor'</span></span>
- <span data-ttu-id="61923-1933">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1934">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1935">'Blazor'</span></span>
- <span data-ttu-id="61923-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1936">'Identity'</span></span>
- <span data-ttu-id="61923-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1938">'Razor'</span></span>
- <span data-ttu-id="61923-1939">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1940">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1941">'Blazor'</span></span>
- <span data-ttu-id="61923-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1942">'Identity'</span></span>
- <span data-ttu-id="61923-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1944">'Razor'</span></span>
- <span data-ttu-id="61923-1945">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1946">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1947">'Blazor'</span></span>
- <span data-ttu-id="61923-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1948">'Identity'</span></span>
- <span data-ttu-id="61923-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1950">'Razor'</span></span>
- <span data-ttu-id="61923-1951">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1952">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1953">'Blazor'</span></span>
- <span data-ttu-id="61923-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1954">'Identity'</span></span>
- <span data-ttu-id="61923-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1956">'Razor'</span></span>
- <span data-ttu-id="61923-1957">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1958">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1959">'Blazor'</span></span>
- <span data-ttu-id="61923-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1960">'Identity'</span></span>
- <span data-ttu-id="61923-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1962">'Razor'</span></span>
- <span data-ttu-id="61923-1963">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1964">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1965">'Blazor'</span></span>
- <span data-ttu-id="61923-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1966">'Identity'</span></span>
- <span data-ttu-id="61923-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1968">'Razor'</span></span>
- <span data-ttu-id="61923-1969">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1970">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1971">'Blazor'</span></span>
- <span data-ttu-id="61923-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1972">'Identity'</span></span>
- <span data-ttu-id="61923-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1974">'Razor'</span></span>
- <span data-ttu-id="61923-1975">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1976">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1977">'Blazor'</span></span>
- <span data-ttu-id="61923-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1978">'Identity'</span></span>
- <span data-ttu-id="61923-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1980">'Razor'</span></span>
- <span data-ttu-id="61923-1981">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1982">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1983">'Blazor'</span></span>
- <span data-ttu-id="61923-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1984">'Identity'</span></span>
- <span data-ttu-id="61923-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1986">'Razor'</span></span>
- <span data-ttu-id="61923-1987">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1988">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1989">'Blazor'</span></span>
- <span data-ttu-id="61923-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1990">'Identity'</span></span>
- <span data-ttu-id="61923-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1992">'Razor'</span></span>
- <span data-ttu-id="61923-1993">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-1994">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-1995">'Blazor'</span></span>
- <span data-ttu-id="61923-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-1996">'Identity'</span></span>
- <span data-ttu-id="61923-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-1998">'Razor'</span></span>
- <span data-ttu-id="61923-1999">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2000">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2001">'Blazor'</span></span>
- <span data-ttu-id="61923-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2002">'Identity'</span></span>
- <span data-ttu-id="61923-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2004">'Razor'</span></span>
- <span data-ttu-id="61923-2005">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2006">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2007">'Blazor'</span></span>
- <span data-ttu-id="61923-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2008">'Identity'</span></span>
- <span data-ttu-id="61923-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2010">'Razor'</span></span>
- <span data-ttu-id="61923-2011">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2012">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2013">'Blazor'</span></span>
- <span data-ttu-id="61923-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2014">'Identity'</span></span>
- <span data-ttu-id="61923-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2016">'Razor'</span></span>
- <span data-ttu-id="61923-2017">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2018">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2019">'Blazor'</span></span>
- <span data-ttu-id="61923-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2020">'Identity'</span></span>
- <span data-ttu-id="61923-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2022">'Razor'</span></span>
- <span data-ttu-id="61923-2023">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2024">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2025">'Blazor'</span></span>
- <span data-ttu-id="61923-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2026">'Identity'</span></span>
- <span data-ttu-id="61923-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2028">'Razor'</span></span>
- <span data-ttu-id="61923-2029">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Wpis konfiguracji nie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="61923-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="61923-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="61923-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="61923-2032">Wartość: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="61923-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="61923-2033">Wartość: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="61923-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="61923-2034">Wartościami`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="61923-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="61923-2035">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="61923-2035">**Example**</span></span>

<span data-ttu-id="61923-2036">Na serwerze zostanie utworzona niestandardowa zmienna środowiskowa parametrów połączenia:</span><span class="sxs-lookup"><span data-stu-id="61923-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="61923-2037">Nazwij`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="61923-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="61923-2038">Wartość:`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="61923-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="61923-2039">Jeśli `IConfiguration` jest wstrzykiwany i przypisany do pola o nazwie `_config` , odczytaj wartość:</span><span class="sxs-lookup"><span data-stu-id="61923-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="61923-2040">Dostawca konfiguracji plików</span><span class="sxs-lookup"><span data-stu-id="61923-2040">File Configuration Provider</span></span>

<span data-ttu-id="61923-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą bazową do ładowania konfiguracji z systemu plików.</span><span class="sxs-lookup"><span data-stu-id="61923-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="61923-2042">Następujący dostawcy konfiguracji są przydzielone do określonych typów plików:</span><span class="sxs-lookup"><span data-stu-id="61923-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="61923-2043">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="61923-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="61923-2044">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="61923-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="61923-2045">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="61923-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="61923-2046">Dostawca konfiguracji pliku INI</span><span class="sxs-lookup"><span data-stu-id="61923-2046">INI Configuration Provider</span></span>

<span data-ttu-id="61923-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku ini w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="61923-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="61923-2048">Aby uaktywnić konfigurację pliku INI, wywołaj <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="61923-2049">Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.</span><span class="sxs-lookup"><span data-stu-id="61923-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="61923-2050">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="61923-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="61923-2051">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="61923-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="61923-2052">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="61923-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="61923-2054">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="61923-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="61923-2055">Ogólny przykład pliku konfiguracji INI:</span><span class="sxs-lookup"><span data-stu-id="61923-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="61923-2056">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="61923-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="61923-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2057">section0:key0</span></span>
* <span data-ttu-id="61923-2058">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2058">section0:key1</span></span>
* <span data-ttu-id="61923-2059">Section1: podsekcja: Key</span><span class="sxs-lookup"><span data-stu-id="61923-2059">section1:subsection:key</span></span>
* <span data-ttu-id="61923-2060">section2: subsection0: klucz</span><span class="sxs-lookup"><span data-stu-id="61923-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="61923-2061">section2: subsection1: klucz</span><span class="sxs-lookup"><span data-stu-id="61923-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="61923-2062">Dostawca konfiguracji JSON</span><span class="sxs-lookup"><span data-stu-id="61923-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="61923-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku JSON podczas środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="61923-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="61923-2064">Aby uaktywnić konfigurację pliku JSON, wywołaj <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="61923-2065">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="61923-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="61923-2066">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="61923-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="61923-2067">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="61923-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="61923-2069">`AddJsonFile`jest automatycznie wywoływana dwukrotnie, gdy nowy Konstruktor hosta zostanie zainicjowany przy użyciu `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="61923-2070">Metoda jest wywoływana w celu załadowania konfiguracji z:</span><span class="sxs-lookup"><span data-stu-id="61923-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="61923-2071">*appSettings. JSON*: plik jest odczytywany jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="61923-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="61923-2072">Wersja środowiska pliku może przesłonić wartości dostarczone przez plik *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="61923-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="61923-2073">*appSettings. {Environment}. JSON*: wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="61923-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="61923-2074">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna](#default-configuration) .</span><span class="sxs-lookup"><span data-stu-id="61923-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="61923-2075">`CreateDefaultBuilder`ładuje również:</span><span class="sxs-lookup"><span data-stu-id="61923-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="61923-2076">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="61923-2076">Environment variables.</span></span>
* <span data-ttu-id="61923-2077">Wpisy [tajne użytkownika (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="61923-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="61923-2078">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="61923-2078">Command-line arguments.</span></span>

<span data-ttu-id="61923-2079">Dostawca konfiguracji JSON został ustanowiony jako pierwszy.</span><span class="sxs-lookup"><span data-stu-id="61923-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="61923-2080">W związku z tym klucze tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia przesłaniają konfigurację ustawioną przez pliki *AppSettings* .</span><span class="sxs-lookup"><span data-stu-id="61923-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="61923-2081">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji dla plików innych niż *appSettings. JSON* i *appSettings. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="61923-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="61923-2082">**Przykład**</span><span class="sxs-lookup"><span data-stu-id="61923-2082">**Example**</span></span>

<span data-ttu-id="61923-2083">Przykładowa aplikacja korzysta z statycznej wygodnej metody `CreateDefaultBuilder` tworzenia hosta, który obejmuje dwa wywołania `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="61923-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="61923-2084">Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="61923-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="61923-2085">Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="61923-2086">Dla *appSettings. Plik Development. JSON* w przykładowej aplikacji jest ładowany:</span><span class="sxs-lookup"><span data-stu-id="61923-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="61923-2087">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="61923-2087">Run the sample app.</span></span> <span data-ttu-id="61923-2088">Otwórz w przeglądarce aplikację pod adresem `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="61923-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="61923-2089">Dane wyjściowe zawierają pary klucz-wartość dla konfiguracji w oparciu o środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="61923-2090">Poziom dziennika klucza `Logging:LogLevel:Default` jest `Debug` używany podczas uruchamiania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="61923-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="61923-2091">Ponownie uruchom przykładową aplikację w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="61923-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="61923-2092">Otwórz plik *Properties/profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="61923-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="61923-2093">W `ConfigurationSample` profilu Zmień wartość `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej na `Production` .</span><span class="sxs-lookup"><span data-stu-id="61923-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="61923-2094">Zapisz plik i uruchom aplikację przy użyciu `dotnet run` powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="61923-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="61923-2095">Ustawienia w *appSettings. Plik Development. JSON* nie zastępuje już ustawień w pliku *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61923-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="61923-2096">Poziom dziennika klucza `Logging:LogLevel:Default` to `Warning` .</span><span class="sxs-lookup"><span data-stu-id="61923-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="61923-2097">Dostawca konfiguracji XML</span><span class="sxs-lookup"><span data-stu-id="61923-2097">XML Configuration Provider</span></span>

<span data-ttu-id="61923-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Ładowanie konfiguracji z par klucz-wartość pliku XML w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="61923-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="61923-2099">Aby uaktywnić konfigurację pliku XML, wywołaj <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="61923-2100">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="61923-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="61923-2101">Czy plik jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="61923-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="61923-2102">Czy konfiguracja zostanie ponownie załadowana w przypadku zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="61923-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Używane do uzyskiwania dostępu do pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="61923-2104">Węzeł główny pliku konfiguracji jest ignorowany, gdy są tworzone pary klucz-wartość konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="61923-2105">Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="61923-2106">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="61923-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="61923-2107">Pliki konfiguracji XML mogą używać odrębnych nazw elementów dla powtarzających się sekcji:</span><span class="sxs-lookup"><span data-stu-id="61923-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="61923-2108">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="61923-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="61923-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2109">section0:key0</span></span>
* <span data-ttu-id="61923-2110">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2110">section0:key1</span></span>
* <span data-ttu-id="61923-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2111">section1:key0</span></span>
* <span data-ttu-id="61923-2112">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2112">section1:key1</span></span>

<span data-ttu-id="61923-2113">Powtarzające się elementy, które używają tej samej nazwy elementu, działają, jeśli `name` atrybut jest używany do odróżnienia elementów:</span><span class="sxs-lookup"><span data-stu-id="61923-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="61923-2114">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="61923-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="61923-2115">sekcja: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="61923-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="61923-2116">sekcja: section0: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="61923-2117">sekcja: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="61923-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="61923-2118">sekcja: Section1: Key: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2118">section:section1:key:key1</span></span>

<span data-ttu-id="61923-2119">Atrybuty mogą służyć do dostarczania wartości:</span><span class="sxs-lookup"><span data-stu-id="61923-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="61923-2120">Poprzedni plik konfiguracji ładuje następujące klucze z `value` :</span><span class="sxs-lookup"><span data-stu-id="61923-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="61923-2121">Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="61923-2121">key:attribute</span></span>
* <span data-ttu-id="61923-2122">sekcja: Key: Attribute</span><span class="sxs-lookup"><span data-stu-id="61923-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="61923-2123">Dostawca konfiguracji klucza dla plików</span><span class="sxs-lookup"><span data-stu-id="61923-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="61923-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Używa plików katalogu jako par klucz konfiguracji i wartość.</span><span class="sxs-lookup"><span data-stu-id="61923-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="61923-2125">Kluczem jest nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2125">The key is the file name.</span></span> <span data-ttu-id="61923-2126">Wartość zawiera zawartość pliku.</span><span class="sxs-lookup"><span data-stu-id="61923-2126">The value contains the file's contents.</span></span> <span data-ttu-id="61923-2127">Dostawca konfiguracji klucza dla plików jest używany w scenariuszach hostingu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="61923-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="61923-2128">Aby uaktywnić konfigurację klucza dla plików, wywołaj <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="61923-2129">`directoryPath`Do plików musi być ścieżką bezwzględną.</span><span class="sxs-lookup"><span data-stu-id="61923-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="61923-2130">Przeciążania Zezwalaj na określanie:</span><span class="sxs-lookup"><span data-stu-id="61923-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="61923-2131">`Action<KeyPerFileConfigurationSource>`Delegat, który konfiguruje źródło.</span><span class="sxs-lookup"><span data-stu-id="61923-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="61923-2132">Określa, czy katalog jest opcjonalny, i ścieżkę do katalogu.</span><span class="sxs-lookup"><span data-stu-id="61923-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="61923-2133">Podwójny znak podkreślenia ( `__` ) jest używany jako ogranicznik klucza konfiguracji w nazwach plików.</span><span class="sxs-lookup"><span data-stu-id="61923-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="61923-2134">Na przykład nazwa pliku `Logging__LogLevel__System` generuje klucz konfiguracji `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="61923-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="61923-2135">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji:</span><span class="sxs-lookup"><span data-stu-id="61923-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="61923-2136">Dostawca konfiguracji pamięci</span><span class="sxs-lookup"><span data-stu-id="61923-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="61923-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Używa kolekcji w pamięci jako par klucz konfiguracji-wartość.</span><span class="sxs-lookup"><span data-stu-id="61923-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="61923-2138">Aby uaktywnić konfigurację kolekcji w pamięci, wywołaj <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodę rozszerzenia w wystąpieniu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="61923-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="61923-2139">Dostawcę konfiguracji można zainicjować przy użyciu `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="61923-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="61923-2140">Wywołaj `ConfigureAppConfiguration` podczas kompilowania hosta, aby określić konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="61923-2141">W poniższym przykładzie tworzony jest słownik konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="61923-2142">Słownik jest używany z wywołaniem do `AddInMemoryCollection` zapewnienia konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="61923-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="61923-2143">GetValue</span></span>

<span data-ttu-id="61923-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji z określonym kluczem i konwertuje ją na określony typ niekolekcje.</span><span class="sxs-lookup"><span data-stu-id="61923-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="61923-2145">Przeciążenie akceptuje wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="61923-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="61923-2146">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="61923-2146">The following example:</span></span>

* <span data-ttu-id="61923-2147">Wyodrębnia wartość ciągu z konfiguracji przy użyciu klucza `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="61923-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="61923-2148">Jeśli `NumberKey` nie znaleziono w kluczach konfiguracji, `99` zostanie użyta wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="61923-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="61923-2149">Typ wartości `int` .</span><span class="sxs-lookup"><span data-stu-id="61923-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="61923-2150">Przechowuje wartość we `NumberConfig` właściwości do użycia na stronie.</span><span class="sxs-lookup"><span data-stu-id="61923-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="61923-2151">GetSection, GetChildren i EXISTS</span><span class="sxs-lookup"><span data-stu-id="61923-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="61923-2152">W poniższych przykładach należy wziąć pod uwagę następujący plik JSON.</span><span class="sxs-lookup"><span data-stu-id="61923-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="61923-2153">Cztery klucze są dostępne w dwóch sekcjach, z których jedna zawiera parę podsekcji:</span><span class="sxs-lookup"><span data-stu-id="61923-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="61923-2154">Gdy plik jest odczytywany do konfiguracji, następujące unikatowe klucze hierarchiczne są tworzone w celu przechowywania wartości konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="61923-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="61923-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2155">section0:key0</span></span>
* <span data-ttu-id="61923-2156">section0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2156">section0:key1</span></span>
* <span data-ttu-id="61923-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2157">section1:key0</span></span>
* <span data-ttu-id="61923-2158">Section1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2158">section1:key1</span></span>
* <span data-ttu-id="61923-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="61923-2160">section2: subsection0: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="61923-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="61923-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="61923-2162">section2: subsection1: Klucz1</span><span class="sxs-lookup"><span data-stu-id="61923-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="61923-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="61923-2163">GetSection</span></span>

<span data-ttu-id="61923-2164">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.</span><span class="sxs-lookup"><span data-stu-id="61923-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="61923-2165">Aby zwrócić element <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierający tylko pary klucz-wartość w `section1` , wywołaniu `GetSection` i podać nazwę sekcji:</span><span class="sxs-lookup"><span data-stu-id="61923-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="61923-2166">`configSection`Nie ma wartości, tylko klucza i ścieżki.</span><span class="sxs-lookup"><span data-stu-id="61923-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="61923-2167">Podobnie Aby uzyskać wartości kluczy w `section2:subsection0` , wywołaj `GetSection` i podaj ścieżkę do sekcji:</span><span class="sxs-lookup"><span data-stu-id="61923-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="61923-2168">`GetSection`nigdy nie zwraca `null` .</span><span class="sxs-lookup"><span data-stu-id="61923-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="61923-2169">Jeśli nie znaleziono pasującej sekcji, `IConfigurationSection` zwracany jest pusty.</span><span class="sxs-lookup"><span data-stu-id="61923-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="61923-2170">Gdy `GetSection` zwraca pasującą sekcję, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nie jest wypełnione.</span><span class="sxs-lookup"><span data-stu-id="61923-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="61923-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> i <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> są zwracane, gdy istnieje sekcja.</span><span class="sxs-lookup"><span data-stu-id="61923-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="61923-2172">GetChildren —</span><span class="sxs-lookup"><span data-stu-id="61923-2172">GetChildren</span></span>

<span data-ttu-id="61923-2173">Wywołanie [iConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) w programie `section2` uzyskuje element `IEnumerable<IConfigurationSection>` obejmujący:</span><span class="sxs-lookup"><span data-stu-id="61923-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="61923-2174">Exists</span><span class="sxs-lookup"><span data-stu-id="61923-2174">Exists</span></span>

<span data-ttu-id="61923-2175">Użyj [ConfigurationExtensions. istnieje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) , aby określić, czy istnieje sekcja konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="61923-2176">Dane przykładowe są spowodowane tym, że `sectionExists` `false` `section2:subsection2` w danych konfiguracji nie ma sekcji.</span><span class="sxs-lookup"><span data-stu-id="61923-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="61923-2177">Powiąż z grafem obiektów</span><span class="sxs-lookup"><span data-stu-id="61923-2177">Bind to an object graph</span></span>

<span data-ttu-id="61923-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>jest w stanie powiązać cały Graf obiektów POCO.</span><span class="sxs-lookup"><span data-stu-id="61923-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="61923-2179">Podobnie jak w przypadku powiązania prostego obiektu, powiązane są tylko publiczne właściwości odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="61923-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="61923-2180">Przykład zawiera `TvShow` model, którego obiekt zawiera obiekty `Metadata` i `Actors` klasy (*modele/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="61923-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="61923-2181">Przykładowa aplikacja zawiera plik *tvshow. XML* zawierający dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="61923-2182">Konfiguracja jest powiązana z `TvShow` wykresem całego obiektu za pomocą `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="61923-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="61923-2183">Powiązane wystąpienie jest przypisane do właściwości w celu renderowania:</span><span class="sxs-lookup"><span data-stu-id="61923-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="61923-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)tworzy powiązania i zwraca określony typ.</span><span class="sxs-lookup"><span data-stu-id="61923-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="61923-2185">`Get<T>`jest wygodniejszy niż używanie `Bind` .</span><span class="sxs-lookup"><span data-stu-id="61923-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="61923-2186">Poniższy kod pokazuje, jak używać `Get<T>` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="61923-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="61923-2187">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="61923-2187">Bind an array to a class</span></span>

<span data-ttu-id="61923-2188">*Przykładowa aplikacja pokazuje Koncepcje opisane w tej sekcji.*</span><span class="sxs-lookup"><span data-stu-id="61923-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="61923-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Obsługuje tablice powiązań z obiektami przy użyciu indeksów tablicowych w kluczach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="61923-2190">Każdy format tablicy, który ujawnia segment klucza numerycznego ( `:0:` , `:1:` , &hellip; `:{n}:` ) jest zdolny do powiązania tablicy z tablicą klas poco.</span><span class="sxs-lookup"><span data-stu-id="61923-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="61923-2191">Powiązanie jest dostarczane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="61923-2191">Binding is provided by convention.</span></span> <span data-ttu-id="61923-2192">Niestandardowi dostawcy konfiguracji nie muszą implementować powiązania tablicy.</span><span class="sxs-lookup"><span data-stu-id="61923-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="61923-2193">**Przetwarzanie tablicy w pamięci**</span><span class="sxs-lookup"><span data-stu-id="61923-2193">**In-memory array processing**</span></span>

<span data-ttu-id="61923-2194">Należy wziąć pod uwagę klucze konfiguracji i wartości podane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="61923-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="61923-2195">Klucz</span><span class="sxs-lookup"><span data-stu-id="61923-2195">Key</span></span>             | <span data-ttu-id="61923-2196">Wartość</span><span class="sxs-lookup"><span data-stu-id="61923-2196">Value</span></span>  |
| :---
<span data-ttu-id="61923-2197">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2198">'Blazor'</span></span>
- <span data-ttu-id="61923-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2199">'Identity'</span></span>
- <span data-ttu-id="61923-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2201">'Razor'</span></span>
- <span data-ttu-id="61923-2202">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2203">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2204">'Blazor'</span></span>
- <span data-ttu-id="61923-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2205">'Identity'</span></span>
- <span data-ttu-id="61923-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2207">'Razor'</span></span>
- <span data-ttu-id="61923-2208">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2209">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2210">'Blazor'</span></span>
- <span data-ttu-id="61923-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2211">'Identity'</span></span>
- <span data-ttu-id="61923-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2213">'Razor'</span></span>
- <span data-ttu-id="61923-2214">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2215">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2216">'Blazor'</span></span>
- <span data-ttu-id="61923-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2217">'Identity'</span></span>
- <span data-ttu-id="61923-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2219">'Razor'</span></span>
- <span data-ttu-id="61923-2220">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2221">-------: | :----: | | Tablica: wpisy: 0 | value0 | | Tablica: wpisy: 1 | wartość1 | | Tablica: wpisy: 2 | wartość2 | | Tablica: wpisy: 4 | value4 | | Tablica: wpisy: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="61923-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="61923-2222">Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:</span><span class="sxs-lookup"><span data-stu-id="61923-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="61923-2223">Tablica pomija wartość dla indeksu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="61923-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="61923-2224">Segregator konfiguracji nie może powiązać wartości null ani tworzyć wpisów o wartości null w obiektach powiązanych, co oznacza, że w chwili pojawi się wynik powiązania tej tablicy z obiektem.</span><span class="sxs-lookup"><span data-stu-id="61923-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="61923-2225">W przykładowej aplikacji jest dostępna Klasa POCO, która przechowuje powiązane dane konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="61923-2226">Dane konfiguracji są powiązane z obiektem:</span><span class="sxs-lookup"><span data-stu-id="61923-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="61923-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)można również użyć składni, co spowoduje zwiększenie kodu kompaktowego:</span><span class="sxs-lookup"><span data-stu-id="61923-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="61923-2228">Obiekt powiązany, wystąpienie elementu `ArrayExample` , otrzymuje dane tablicy z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="61923-2229">`ArrayExample.Entries`Indeks</span><span class="sxs-lookup"><span data-stu-id="61923-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="61923-2230">`ArrayExample.Entries`Wartościami</span><span class="sxs-lookup"><span data-stu-id="61923-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="61923-2231">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2232">'Blazor'</span></span>
- <span data-ttu-id="61923-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2233">'Identity'</span></span>
- <span data-ttu-id="61923-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2235">'Razor'</span></span>
- <span data-ttu-id="61923-2236">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2237">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2238">'Blazor'</span></span>
- <span data-ttu-id="61923-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2239">'Identity'</span></span>
- <span data-ttu-id="61923-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2241">'Razor'</span></span>
- <span data-ttu-id="61923-2242">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2243">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2244">'Blazor'</span></span>
- <span data-ttu-id="61923-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2245">'Identity'</span></span>
- <span data-ttu-id="61923-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2247">'Razor'</span></span>
- <span data-ttu-id="61923-2248">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2249">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2250">'Blazor'</span></span>
- <span data-ttu-id="61923-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2251">'Identity'</span></span>
- <span data-ttu-id="61923-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2253">'Razor'</span></span>
- <span data-ttu-id="61923-2254">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2255">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2256">'Blazor'</span></span>
- <span data-ttu-id="61923-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2257">'Identity'</span></span>
- <span data-ttu-id="61923-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2259">'Razor'</span></span>
- <span data-ttu-id="61923-2260">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2261">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2262">'Blazor'</span></span>
- <span data-ttu-id="61923-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2263">'Identity'</span></span>
- <span data-ttu-id="61923-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2265">'Razor'</span></span>
- <span data-ttu-id="61923-2266">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2267">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2268">'Blazor'</span></span>
- <span data-ttu-id="61923-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2269">'Identity'</span></span>
- <span data-ttu-id="61923-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2271">'Razor'</span></span>
- <span data-ttu-id="61923-2272">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2273">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2274">'Blazor'</span></span>
- <span data-ttu-id="61923-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2275">'Identity'</span></span>
- <span data-ttu-id="61923-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2277">'Razor'</span></span>
- <span data-ttu-id="61923-2278">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2279">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2280">'Blazor'</span></span>
- <span data-ttu-id="61923-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2281">'Identity'</span></span>
- <span data-ttu-id="61923-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2283">'Razor'</span></span>
- <span data-ttu-id="61923-2284">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2285">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2286">'Blazor'</span></span>
- <span data-ttu-id="61923-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2287">'Identity'</span></span>
- <span data-ttu-id="61923-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2289">'Razor'</span></span>
- <span data-ttu-id="61923-2290">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2291">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2292">'Blazor'</span></span>
- <span data-ttu-id="61923-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2293">'Identity'</span></span>
- <span data-ttu-id="61923-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2295">'Razor'</span></span>
- <span data-ttu-id="61923-2296">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2297">-------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2298">'Blazor'</span></span>
- <span data-ttu-id="61923-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2299">'Identity'</span></span>
- <span data-ttu-id="61923-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2301">'Razor'</span></span>
- <span data-ttu-id="61923-2302">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2303">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2304">'Blazor'</span></span>
- <span data-ttu-id="61923-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2305">'Identity'</span></span>
- <span data-ttu-id="61923-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2307">'Razor'</span></span>
- <span data-ttu-id="61923-2308">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2309">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2310">'Blazor'</span></span>
- <span data-ttu-id="61923-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2311">'Identity'</span></span>
- <span data-ttu-id="61923-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2313">'Razor'</span></span>
- <span data-ttu-id="61923-2314">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2315">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2316">'Blazor'</span></span>
- <span data-ttu-id="61923-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2317">'Identity'</span></span>
- <span data-ttu-id="61923-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2319">'Razor'</span></span>
- <span data-ttu-id="61923-2320">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2321">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2322">'Blazor'</span></span>
- <span data-ttu-id="61923-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2323">'Identity'</span></span>
- <span data-ttu-id="61923-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2325">'Razor'</span></span>
- <span data-ttu-id="61923-2326">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2327">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2328">'Blazor'</span></span>
- <span data-ttu-id="61923-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2329">'Identity'</span></span>
- <span data-ttu-id="61923-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2331">'Razor'</span></span>
- <span data-ttu-id="61923-2332">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2333">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2334">'Blazor'</span></span>
- <span data-ttu-id="61923-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2335">'Identity'</span></span>
- <span data-ttu-id="61923-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2337">'Razor'</span></span>
- <span data-ttu-id="61923-2338">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2339">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2340">'Blazor'</span></span>
- <span data-ttu-id="61923-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2341">'Identity'</span></span>
- <span data-ttu-id="61923-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2343">'Razor'</span></span>
- <span data-ttu-id="61923-2344">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2345">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2346">'Blazor'</span></span>
- <span data-ttu-id="61923-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2347">'Identity'</span></span>
- <span data-ttu-id="61923-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2349">'Razor'</span></span>
- <span data-ttu-id="61923-2350">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2351">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2352">'Blazor'</span></span>
- <span data-ttu-id="61923-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2353">'Identity'</span></span>
- <span data-ttu-id="61923-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2355">'Razor'</span></span>
- <span data-ttu-id="61923-2356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2357">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2358">'Blazor'</span></span>
- <span data-ttu-id="61923-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2359">'Identity'</span></span>
- <span data-ttu-id="61923-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2361">'Razor'</span></span>
- <span data-ttu-id="61923-2362">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2363">-------------: | | 0 | value0 | | 1 | wartość1 | | 2 | wartość2 | | 3 | value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="61923-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="61923-2364">Indeks &num; 3 w obiekcie powiązanym przechowuje dane konfiguracji dla `array:4` klucza konfiguracji i jego wartość `value4` .</span><span class="sxs-lookup"><span data-stu-id="61923-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="61923-2365">Gdy dane konfiguracji zawierające tablicę są powiązane, indeksy tablic w kluczach konfiguracji są używane tylko do iteracji danych konfiguracji podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="61923-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="61923-2366">Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracji pomija jeden lub więcej indeksów.</span><span class="sxs-lookup"><span data-stu-id="61923-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="61923-2367">Brakujący element konfiguracji dla indeksu &num; 3 można podać przed powiązaniem z `ArrayExample` wystąpieniem przez dowolnego dostawcę konfiguracji, który generuje poprawną parę klucz-wartość w konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="61923-2368">Jeśli przykład zawiera dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, `ArrayExample.Entries` dopasowuje pełną tablicę konfiguracyjną:</span><span class="sxs-lookup"><span data-stu-id="61923-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="61923-2369">plik *missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="61923-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="61923-2370">W pliku `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="61923-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="61923-2371">Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="61923-2372">Klucz</span><span class="sxs-lookup"><span data-stu-id="61923-2372">Key</span></span>             | <span data-ttu-id="61923-2373">Wartość</span><span class="sxs-lookup"><span data-stu-id="61923-2373">Value</span></span>  |
| :---
<span data-ttu-id="61923-2374">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2375">'Blazor'</span></span>
- <span data-ttu-id="61923-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2376">'Identity'</span></span>
- <span data-ttu-id="61923-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2378">'Razor'</span></span>
- <span data-ttu-id="61923-2379">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2380">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2381">'Blazor'</span></span>
- <span data-ttu-id="61923-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2382">'Identity'</span></span>
- <span data-ttu-id="61923-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2384">'Razor'</span></span>
- <span data-ttu-id="61923-2385">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2386">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2387">'Blazor'</span></span>
- <span data-ttu-id="61923-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2388">'Identity'</span></span>
- <span data-ttu-id="61923-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2390">'Razor'</span></span>
- <span data-ttu-id="61923-2391">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2392">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2393">'Blazor'</span></span>
- <span data-ttu-id="61923-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2394">'Identity'</span></span>
- <span data-ttu-id="61923-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2396">'Razor'</span></span>
- <span data-ttu-id="61923-2397">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2398">-------: | :----: | | Tablica: wpisy: 3 | Wartość3 |</span><span class="sxs-lookup"><span data-stu-id="61923-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="61923-2399">Jeśli `ArrayExample` wystąpienie klasy jest powiązane, gdy dostawca konfiguracji JSON zawiera wpis dla indeksu &num; 3, `ArrayExample.Entries` Tablica zawiera wartość.</span><span class="sxs-lookup"><span data-stu-id="61923-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="61923-2400">`ArrayExample.Entries`Indeks</span><span class="sxs-lookup"><span data-stu-id="61923-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="61923-2401">`ArrayExample.Entries`Wartościami</span><span class="sxs-lookup"><span data-stu-id="61923-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="61923-2402">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2403">'Blazor'</span></span>
- <span data-ttu-id="61923-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2404">'Identity'</span></span>
- <span data-ttu-id="61923-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2406">'Razor'</span></span>
- <span data-ttu-id="61923-2407">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2408">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2409">'Blazor'</span></span>
- <span data-ttu-id="61923-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2410">'Identity'</span></span>
- <span data-ttu-id="61923-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2412">'Razor'</span></span>
- <span data-ttu-id="61923-2413">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2414">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2415">'Blazor'</span></span>
- <span data-ttu-id="61923-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2416">'Identity'</span></span>
- <span data-ttu-id="61923-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2418">'Razor'</span></span>
- <span data-ttu-id="61923-2419">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2420">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2421">'Blazor'</span></span>
- <span data-ttu-id="61923-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2422">'Identity'</span></span>
- <span data-ttu-id="61923-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2424">'Razor'</span></span>
- <span data-ttu-id="61923-2425">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2426">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2427">'Blazor'</span></span>
- <span data-ttu-id="61923-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2428">'Identity'</span></span>
- <span data-ttu-id="61923-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2430">'Razor'</span></span>
- <span data-ttu-id="61923-2431">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2432">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2433">'Blazor'</span></span>
- <span data-ttu-id="61923-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2434">'Identity'</span></span>
- <span data-ttu-id="61923-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2436">'Razor'</span></span>
- <span data-ttu-id="61923-2437">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2438">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2439">'Blazor'</span></span>
- <span data-ttu-id="61923-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2440">'Identity'</span></span>
- <span data-ttu-id="61923-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2442">'Razor'</span></span>
- <span data-ttu-id="61923-2443">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2444">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2445">'Blazor'</span></span>
- <span data-ttu-id="61923-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2446">'Identity'</span></span>
- <span data-ttu-id="61923-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2448">'Razor'</span></span>
- <span data-ttu-id="61923-2449">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2450">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2451">'Blazor'</span></span>
- <span data-ttu-id="61923-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2452">'Identity'</span></span>
- <span data-ttu-id="61923-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2454">'Razor'</span></span>
- <span data-ttu-id="61923-2455">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2456">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2457">'Blazor'</span></span>
- <span data-ttu-id="61923-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2458">'Identity'</span></span>
- <span data-ttu-id="61923-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2460">'Razor'</span></span>
- <span data-ttu-id="61923-2461">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2462">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2463">'Blazor'</span></span>
- <span data-ttu-id="61923-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2464">'Identity'</span></span>
- <span data-ttu-id="61923-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2466">'Razor'</span></span>
- <span data-ttu-id="61923-2467">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2468">-------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2469">'Blazor'</span></span>
- <span data-ttu-id="61923-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2470">'Identity'</span></span>
- <span data-ttu-id="61923-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2472">'Razor'</span></span>
- <span data-ttu-id="61923-2473">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2474">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2475">'Blazor'</span></span>
- <span data-ttu-id="61923-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2476">'Identity'</span></span>
- <span data-ttu-id="61923-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2478">'Razor'</span></span>
- <span data-ttu-id="61923-2479">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2480">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2481">'Blazor'</span></span>
- <span data-ttu-id="61923-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2482">'Identity'</span></span>
- <span data-ttu-id="61923-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2484">'Razor'</span></span>
- <span data-ttu-id="61923-2485">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2486">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2487">'Blazor'</span></span>
- <span data-ttu-id="61923-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2488">'Identity'</span></span>
- <span data-ttu-id="61923-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2490">'Razor'</span></span>
- <span data-ttu-id="61923-2491">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2492">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2493">'Blazor'</span></span>
- <span data-ttu-id="61923-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2494">'Identity'</span></span>
- <span data-ttu-id="61923-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2496">'Razor'</span></span>
- <span data-ttu-id="61923-2497">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2498">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2499">'Blazor'</span></span>
- <span data-ttu-id="61923-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2500">'Identity'</span></span>
- <span data-ttu-id="61923-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2502">'Razor'</span></span>
- <span data-ttu-id="61923-2503">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2504">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2505">'Blazor'</span></span>
- <span data-ttu-id="61923-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2506">'Identity'</span></span>
- <span data-ttu-id="61923-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2508">'Razor'</span></span>
- <span data-ttu-id="61923-2509">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2510">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2511">'Blazor'</span></span>
- <span data-ttu-id="61923-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2512">'Identity'</span></span>
- <span data-ttu-id="61923-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2514">'Razor'</span></span>
- <span data-ttu-id="61923-2515">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2516">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2517">'Blazor'</span></span>
- <span data-ttu-id="61923-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2518">'Identity'</span></span>
- <span data-ttu-id="61923-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2520">'Razor'</span></span>
- <span data-ttu-id="61923-2521">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2522">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2523">'Blazor'</span></span>
- <span data-ttu-id="61923-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2524">'Identity'</span></span>
- <span data-ttu-id="61923-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2526">'Razor'</span></span>
- <span data-ttu-id="61923-2527">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2528">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2529">'Blazor'</span></span>
- <span data-ttu-id="61923-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2530">'Identity'</span></span>
- <span data-ttu-id="61923-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2532">'Razor'</span></span>
- <span data-ttu-id="61923-2533">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2534">-------------: | | 0 | value0 | | 1 | wartość1 | | 2 | wartość2 | | 3 | Wartość3 | | 4 | value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="61923-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="61923-2535">**Przetwarzanie tablicy JSON**</span><span class="sxs-lookup"><span data-stu-id="61923-2535">**JSON array processing**</span></span>

<span data-ttu-id="61923-2536">Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji o wartości zero.</span><span class="sxs-lookup"><span data-stu-id="61923-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="61923-2537">W poniższym pliku konfiguracji `subsection` jest tablicą:</span><span class="sxs-lookup"><span data-stu-id="61923-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="61923-2538">Dostawca konfiguracji JSON odczytuje dane konfiguracji do następujących par klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="61923-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="61923-2539">Klucz</span><span class="sxs-lookup"><span data-stu-id="61923-2539">Key</span></span>                     | <span data-ttu-id="61923-2540">Wartość</span><span class="sxs-lookup"><span data-stu-id="61923-2540">Value</span></span>  |
| ---
<span data-ttu-id="61923-2541">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2542">'Blazor'</span></span>
- <span data-ttu-id="61923-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2543">'Identity'</span></span>
- <span data-ttu-id="61923-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2545">'Razor'</span></span>
- <span data-ttu-id="61923-2546">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2547">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2548">'Blazor'</span></span>
- <span data-ttu-id="61923-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2549">'Identity'</span></span>
- <span data-ttu-id="61923-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2551">'Razor'</span></span>
- <span data-ttu-id="61923-2552">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2553">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2554">'Blazor'</span></span>
- <span data-ttu-id="61923-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2555">'Identity'</span></span>
- <span data-ttu-id="61923-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2557">'Razor'</span></span>
- <span data-ttu-id="61923-2558">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2559">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2560">'Blazor'</span></span>
- <span data-ttu-id="61923-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2561">'Identity'</span></span>
- <span data-ttu-id="61923-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2563">'Razor'</span></span>
- <span data-ttu-id="61923-2564">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2565">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2566">'Blazor'</span></span>
- <span data-ttu-id="61923-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2567">'Identity'</span></span>
- <span data-ttu-id="61923-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2569">'Razor'</span></span>
- <span data-ttu-id="61923-2570">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2571">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2572">'Blazor'</span></span>
- <span data-ttu-id="61923-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2573">'Identity'</span></span>
- <span data-ttu-id="61923-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2575">'Razor'</span></span>
- <span data-ttu-id="61923-2576">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2577">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2578">'Blazor'</span></span>
- <span data-ttu-id="61923-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2579">'Identity'</span></span>
- <span data-ttu-id="61923-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2581">'Razor'</span></span>
- <span data-ttu-id="61923-2582">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2583">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2584">'Blazor'</span></span>
- <span data-ttu-id="61923-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2585">'Identity'</span></span>
- <span data-ttu-id="61923-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2587">'Razor'</span></span>
- <span data-ttu-id="61923-2588">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2589">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2590">'Blazor'</span></span>
- <span data-ttu-id="61923-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2591">'Identity'</span></span>
- <span data-ttu-id="61923-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2593">'Razor'</span></span>
- <span data-ttu-id="61923-2594">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2595">------------ | :----: | | json_array: klucz | wartość a | | json_array: podsekcja: 0 | Wartośćb | | json_array: podsekcja: 1 | valueC | | json_array: podsekcja: 2 | Wartościowe |</span><span class="sxs-lookup"><span data-stu-id="61923-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="61923-2596">W przykładowej aplikacji jest dostępna następująca Klasa POCO z powiązaniem par klucz-wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="61923-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="61923-2597">Po powiązaniu `JsonArrayExample.Key` utrzymuje wartość `valueA` .</span><span class="sxs-lookup"><span data-stu-id="61923-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="61923-2598">Wartości podsekcji są przechowywane we właściwości tablicy POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="61923-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="61923-2599">`JsonArrayExample.Subsection`Indeks</span><span class="sxs-lookup"><span data-stu-id="61923-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="61923-2600">`JsonArrayExample.Subsection`Wartościami</span><span class="sxs-lookup"><span data-stu-id="61923-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="61923-2601">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2602">'Blazor'</span></span>
- <span data-ttu-id="61923-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2603">'Identity'</span></span>
- <span data-ttu-id="61923-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2605">'Razor'</span></span>
- <span data-ttu-id="61923-2606">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2607">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2608">'Blazor'</span></span>
- <span data-ttu-id="61923-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2609">'Identity'</span></span>
- <span data-ttu-id="61923-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2611">'Razor'</span></span>
- <span data-ttu-id="61923-2612">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2613">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2614">'Blazor'</span></span>
- <span data-ttu-id="61923-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2615">'Identity'</span></span>
- <span data-ttu-id="61923-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2617">'Razor'</span></span>
- <span data-ttu-id="61923-2618">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2619">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2620">'Blazor'</span></span>
- <span data-ttu-id="61923-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2621">'Identity'</span></span>
- <span data-ttu-id="61923-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2623">'Razor'</span></span>
- <span data-ttu-id="61923-2624">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2625">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2626">'Blazor'</span></span>
- <span data-ttu-id="61923-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2627">'Identity'</span></span>
- <span data-ttu-id="61923-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2629">'Razor'</span></span>
- <span data-ttu-id="61923-2630">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2631">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2632">'Blazor'</span></span>
- <span data-ttu-id="61923-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2633">'Identity'</span></span>
- <span data-ttu-id="61923-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2635">'Razor'</span></span>
- <span data-ttu-id="61923-2636">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2637">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2638">'Blazor'</span></span>
- <span data-ttu-id="61923-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2639">'Identity'</span></span>
- <span data-ttu-id="61923-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2641">'Razor'</span></span>
- <span data-ttu-id="61923-2642">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2643">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2644">'Blazor'</span></span>
- <span data-ttu-id="61923-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2645">'Identity'</span></span>
- <span data-ttu-id="61923-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2647">'Razor'</span></span>
- <span data-ttu-id="61923-2648">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2649">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2650">'Blazor'</span></span>
- <span data-ttu-id="61923-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2651">'Identity'</span></span>
- <span data-ttu-id="61923-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2653">'Razor'</span></span>
- <span data-ttu-id="61923-2654">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2655">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2656">'Blazor'</span></span>
- <span data-ttu-id="61923-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2657">'Identity'</span></span>
- <span data-ttu-id="61923-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2659">'Razor'</span></span>
- <span data-ttu-id="61923-2660">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2661">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2662">'Blazor'</span></span>
- <span data-ttu-id="61923-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2663">'Identity'</span></span>
- <span data-ttu-id="61923-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2665">'Razor'</span></span>
- <span data-ttu-id="61923-2666">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2667">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2668">'Blazor'</span></span>
- <span data-ttu-id="61923-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2669">'Identity'</span></span>
- <span data-ttu-id="61923-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2671">'Razor'</span></span>
- <span data-ttu-id="61923-2672">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2673">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2674">'Blazor'</span></span>
- <span data-ttu-id="61923-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2675">'Identity'</span></span>
- <span data-ttu-id="61923-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2677">'Razor'</span></span>
- <span data-ttu-id="61923-2678">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2679">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2680">'Blazor'</span></span>
- <span data-ttu-id="61923-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2681">'Identity'</span></span>
- <span data-ttu-id="61923-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2683">'Razor'</span></span>
- <span data-ttu-id="61923-2684">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2685">-----------------: | :---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2686">'Blazor'</span></span>
- <span data-ttu-id="61923-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2687">'Identity'</span></span>
- <span data-ttu-id="61923-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2689">'Razor'</span></span>
- <span data-ttu-id="61923-2690">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2691">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2692">'Blazor'</span></span>
- <span data-ttu-id="61923-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2693">'Identity'</span></span>
- <span data-ttu-id="61923-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2695">'Razor'</span></span>
- <span data-ttu-id="61923-2696">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2697">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2698">'Blazor'</span></span>
- <span data-ttu-id="61923-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2699">'Identity'</span></span>
- <span data-ttu-id="61923-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2701">'Razor'</span></span>
- <span data-ttu-id="61923-2702">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2703">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2704">'Blazor'</span></span>
- <span data-ttu-id="61923-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2705">'Identity'</span></span>
- <span data-ttu-id="61923-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2707">'Razor'</span></span>
- <span data-ttu-id="61923-2708">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2709">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2710">'Blazor'</span></span>
- <span data-ttu-id="61923-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2711">'Identity'</span></span>
- <span data-ttu-id="61923-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2713">'Razor'</span></span>
- <span data-ttu-id="61923-2714">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2715">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2716">'Blazor'</span></span>
- <span data-ttu-id="61923-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2717">'Identity'</span></span>
- <span data-ttu-id="61923-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2719">'Razor'</span></span>
- <span data-ttu-id="61923-2720">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2721">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2722">'Blazor'</span></span>
- <span data-ttu-id="61923-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2723">'Identity'</span></span>
- <span data-ttu-id="61923-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2725">'Razor'</span></span>
- <span data-ttu-id="61923-2726">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2727">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2728">'Blazor'</span></span>
- <span data-ttu-id="61923-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2729">'Identity'</span></span>
- <span data-ttu-id="61923-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2731">'Razor'</span></span>
- <span data-ttu-id="61923-2732">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2733">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2734">'Blazor'</span></span>
- <span data-ttu-id="61923-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2735">'Identity'</span></span>
- <span data-ttu-id="61923-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2737">'Razor'</span></span>
- <span data-ttu-id="61923-2738">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2739">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2740">'Blazor'</span></span>
- <span data-ttu-id="61923-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2741">'Identity'</span></span>
- <span data-ttu-id="61923-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2743">'Razor'</span></span>
- <span data-ttu-id="61923-2744">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2745">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2746">'Blazor'</span></span>
- <span data-ttu-id="61923-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2747">'Identity'</span></span>
- <span data-ttu-id="61923-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2749">'Razor'</span></span>
- <span data-ttu-id="61923-2750">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2751">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2752">'Blazor'</span></span>
- <span data-ttu-id="61923-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2753">'Identity'</span></span>
- <span data-ttu-id="61923-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2755">'Razor'</span></span>
- <span data-ttu-id="61923-2756">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2757">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2758">'Blazor'</span></span>
- <span data-ttu-id="61923-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2759">'Identity'</span></span>
- <span data-ttu-id="61923-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2761">'Razor'</span></span>
- <span data-ttu-id="61923-2762">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="61923-2763">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="61923-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="61923-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="61923-2764">'Blazor'</span></span>
- <span data-ttu-id="61923-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="61923-2765">'Identity'</span></span>
- <span data-ttu-id="61923-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="61923-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="61923-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="61923-2767">'Razor'</span></span>
- <span data-ttu-id="61923-2768">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="61923-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="61923-2769">-----------------: | | 0 | Wartośćb | | 1 | valueC | | 2 | Wartościowe |</span><span class="sxs-lookup"><span data-stu-id="61923-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="61923-2770">Niestandardowy dostawca konfiguracji</span><span class="sxs-lookup"><span data-stu-id="61923-2770">Custom configuration provider</span></span>

<span data-ttu-id="61923-2771">Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucz-wartość konfiguracji z bazy danych przy użyciu [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="61923-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="61923-2772">Dostawca ma następującą charakterystykę:</span><span class="sxs-lookup"><span data-stu-id="61923-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="61923-2773">Baza danych EF w pamięci jest używana w celach demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="61923-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="61923-2774">Aby użyć bazy danych, która wymaga parametrów połączenia, zaimplementuj dodatkową wartość w `ConfigurationBuilder` celu dostarczenia parametrów połączenia od innego dostawcy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="61923-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="61923-2775">Dostawca odczytuje tabelę bazy danych w konfiguracji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="61923-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="61923-2776">Dostawca nie wykonuje zapytania do bazy danych w oparciu o klucz.</span><span class="sxs-lookup"><span data-stu-id="61923-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="61923-2777">Ponowne załadowanie nie zostało zaimplementowane, więc aktualizacja bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="61923-2778">Zdefiniuj `EFConfigurationValue` jednostkę do przechowywania wartości konfiguracji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="61923-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="61923-2779">*Modele/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="61923-2780">Dodaj `EFConfigurationContext` do magazynu i uzyskaj dostęp do skonfigurowanych wartości.</span><span class="sxs-lookup"><span data-stu-id="61923-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="61923-2781">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="61923-2782">Utwórz klasę implementującą <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="61923-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="61923-2783">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="61923-2784">Utwórz niestandardowego dostawcę konfiguracji, dziedziczących od <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="61923-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="61923-2785">Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.</span><span class="sxs-lookup"><span data-stu-id="61923-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="61923-2786">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="61923-2787">`AddEFConfiguration`Metoda rozszerzająca zezwala na Dodawanie źródła konfiguracji do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="61923-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="61923-2788">*Rozszerzenia/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="61923-2789">Poniższy kod pokazuje, jak używać niestandardowych `EFConfigurationProvider` w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="61923-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="61923-2790">Konfiguracja dostępu podczas uruchamiania</span><span class="sxs-lookup"><span data-stu-id="61923-2790">Access configuration during startup</span></span>

<span data-ttu-id="61923-2791">Wsuń `IConfiguration` do `Startup` konstruktora, aby uzyskać dostęp do wartości konfiguracyjnych w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="61923-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="61923-2792">Aby uzyskać dostęp do konfiguracji w programie `Startup.Configure` , należy wstrzyknąć `IConfiguration` bezpośrednio do metody lub użyć wystąpienia z konstruktora:</span><span class="sxs-lookup"><span data-stu-id="61923-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="61923-2793">Aby zapoznać się z przykładem uzyskiwania dostępu do konfiguracji przy użyciu metod uruchamiania, zobacz [Uruchamianie aplikacji: wygodne metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="61923-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="61923-2794">Konfiguracja dostępu na Razor stronie stron lub widoku MVC</span><span class="sxs-lookup"><span data-stu-id="61923-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="61923-2795">Aby uzyskać dostęp do ustawień konfiguracji na Razor stronie stron lub widoku MVC, Dodaj [dyrektywę using](xref:mvc/views/razor#using) ([odwołanie w C#: Using](/dotnet/csharp/language-reference/keywords/using-directive)) dla [przestrzeni nazw Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) i wsuń <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="61923-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="61923-2796">Na Razor stronie stron:</span><span class="sxs-lookup"><span data-stu-id="61923-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="61923-2797">W widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="61923-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="61923-2798">Dodawanie konfiguracji z zestawu zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="61923-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="61923-2799"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania z zewnętrznego zestawu poza `Startup` klasą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="61923-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="61923-2800">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="61923-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61923-2801">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="61923-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
