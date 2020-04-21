---
title: Bezpieczne przechowywanie tajemnic aplikacji w rozwoju w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak przechowywać i pobierać poufne informacje jako wpisy tajne aplikacji podczas tworzenia aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661804"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="5ece5-103">Bezpieczne przechowywanie tajemnic aplikacji w rozwoju w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ece5-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ece5-104">Rick [Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5ece5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5ece5-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5ece5-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5ece5-106">W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="5ece5-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5ece5-107">Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie źródłowym.</span><span class="sxs-lookup"><span data-stu-id="5ece5-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5ece5-108">Wpisy tajne produkcji nie powinny być używane do programowania lub testowania.</span><span class="sxs-lookup"><span data-stu-id="5ece5-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5ece5-109">Wpisy tajne nie powinny być wdrażane z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="5ece5-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5ece5-110">Zamiast tego wpisy tajne powinny być udostępniane w środowisku produkcyjnym za pomocą kontrolowanych środków, takich jak zmienne środowiskowe, usługa Azure Key Vault itp. Wpisy tajne testów i produkcji platformy Azure można przechowywać i chronić za pomocą [dostawcy konfiguracji usługi Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="5ece5-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5ece5-111">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="5ece5-111">Environment variables</span></span>

<span data-ttu-id="5ece5-112">Zmienne środowiskowe są używane w celu uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="5ece5-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5ece5-113">Zmienne środowiskowe zastępują wartości konfiguracji dla wszystkich wcześniej określonych źródeł konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5ece5-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5ece5-114">Należy wziąć pod uwagę ASP.NET podstawowej aplikacji sieci web, w której zabezpieczenia **poszczególnych kont użytkowników** jest włączona.</span><span class="sxs-lookup"><span data-stu-id="5ece5-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5ece5-115">Domyślny ciąg połączenia bazy danych znajduje się w pliku *appsettings.json* projektu z kluczem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="5ece5-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5ece5-116">Domyślny ciąg połączenia jest dla LocalDB, który działa w trybie użytkownika i nie wymaga hasła.</span><span class="sxs-lookup"><span data-stu-id="5ece5-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5ece5-117">Podczas wdrażania aplikacji `DefaultConnection` wartość klucza można zastąpić wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="5ece5-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5ece5-118">Zmienna środowiskowa może przechowywać pełny ciąg połączenia z poufnymi poświadczeniami.</span><span class="sxs-lookup"><span data-stu-id="5ece5-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5ece5-119">Zmienne środowiskowe są zazwyczaj przechowywane w zwykłym, niezaszyfrowanym tekście.</span><span class="sxs-lookup"><span data-stu-id="5ece5-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5ece5-120">Jeśli maszyna lub proces zostanie naruszona, zmienne środowiskowe są dostępne dla niezaufanych stron.</span><span class="sxs-lookup"><span data-stu-id="5ece5-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5ece5-121">Mogą być wymagane dodatkowe środki zapobiegające ujawnieniu informacji o tajemnicach użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5ece5-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5ece5-122">Tajny menedżer</span><span class="sxs-lookup"><span data-stu-id="5ece5-122">Secret Manager</span></span>

<span data-ttu-id="5ece5-123">Narzędzie Secret Manager przechowuje poufne dane podczas opracowywania projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5ece5-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5ece5-124">W tym kontekście fragment poufnych danych jest kluczem tajnym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5ece5-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5ece5-125">Wpisy tajne aplikacji są przechowywane w osobnej lokalizacji od drzewa projektu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5ece5-126">Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w kilku projektach.</span><span class="sxs-lookup"><span data-stu-id="5ece5-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5ece5-127">Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="5ece5-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5ece5-128">Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinno być traktowane jako zaufany magazyn.</span><span class="sxs-lookup"><span data-stu-id="5ece5-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5ece5-129">To tylko do celów rozwojowych.</span><span class="sxs-lookup"><span data-stu-id="5ece5-129">It's for development purposes only.</span></span> <span data-ttu-id="5ece5-130">Klucze i wartości są przechowywane w pliku konfiguracyjnym JSON w katalogu profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5ece5-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5ece5-131">Jak działa narzędzie Secret Manager</span><span class="sxs-lookup"><span data-stu-id="5ece5-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="5ece5-132">Narzędzie Secret Manager odsuń szczegóły implementacji, takie jak gdzie i jak wartości są przechowywane.</span><span class="sxs-lookup"><span data-stu-id="5ece5-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5ece5-133">Można użyć narzędzia bez znajomości tych szczegółów implementacji.</span><span class="sxs-lookup"><span data-stu-id="5ece5-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5ece5-134">Wartości są przechowywane w pliku konfiguracyjnym JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:</span><span class="sxs-lookup"><span data-stu-id="5ece5-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5ece5-135">Windows</span><span class="sxs-lookup"><span data-stu-id="5ece5-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="5ece5-136">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="5ece5-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5ece5-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5ece5-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5ece5-138">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="5ece5-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5ece5-139">W poprzednich ścieżkach plików `<user_secrets_id>` zastąp wartością `UserSecretsId` określoną w pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5ece5-140">Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisanych za pomocą narzędzia Menedżer tajny.</span><span class="sxs-lookup"><span data-stu-id="5ece5-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5ece5-141">Te szczegóły implementacji mogą ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="5ece5-141">These implementation details may change.</span></span> <span data-ttu-id="5ece5-142">Na przykład wartości tajne nie są szyfrowane, ale może być w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="5ece5-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5ece5-143">Włączanie pamięci masowej tajnej</span><span class="sxs-lookup"><span data-stu-id="5ece5-143">Enable secret storage</span></span>

<span data-ttu-id="5ece5-144">Narzędzie Secret Manager działa na ustawieniach konfiguracji specyficznych dla projektu przechowywanych w profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5ece5-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5ece5-145">Narzędzie Secret Manager `init` zawiera polecenie w pliku .NET Core SDK 3.0.100 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="5ece5-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="5ece5-146">Aby użyć wpisów tajnych użytkownika, uruchom następujące polecenie w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="5ece5-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="5ece5-147">Poprzednie polecenie dodaje `UserSecretsId` element w `PropertyGroup` pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5ece5-148">Domyślnie wewnętrzny tekst `UserSecretsId` jest identyfikatorem GUID.</span><span class="sxs-lookup"><span data-stu-id="5ece5-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="5ece5-149">Tekst wewnętrzny jest dowolny, ale jest unikatowy dla projektu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="5ece5-150">W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **Zarządzaj wpisami tajnymi użytkowników** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="5ece5-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5ece5-151">Ten gest `UserSecretsId` dodaje element wypełniony identyfikatorem GUID do pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5ece5-152">Ustawianie klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="5ece5-152">Set a secret</span></span>

<span data-ttu-id="5ece5-153">Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości.</span><span class="sxs-lookup"><span data-stu-id="5ece5-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5ece5-154">Klucz tajny jest skojarzony z `UserSecretsId` wartością projektu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5ece5-155">Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5ece5-156">W poprzednim przykładzie dwukropek oznacza, że `Movies` jest obiektem dosłownym z właściwością. `ServiceApiKey`</span><span class="sxs-lookup"><span data-stu-id="5ece5-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5ece5-157">Narzędzie Secret Manager może być również używane z innych katalogów.</span><span class="sxs-lookup"><span data-stu-id="5ece5-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5ece5-158">Użyj `--project` tej opcji, aby podać ścieżkę systemu plików, przy której istnieje plik *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5ece5-159">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5ece5-160">Spłaszczanie struktury JSON w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ece5-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5ece5-161">Gest Zarządzanie **wpisami tajnymi użytkowników** w programie Visual Studio otwiera plik *secrets.json* w edytorze tekstu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5ece5-162">Zastąp zawartość *pliku secrets.json* parami klucz-wartość, które mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="5ece5-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5ece5-163">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5ece5-164">Struktura JSON jest spłaszczana po `dotnet user-secrets remove` `dotnet user-secrets set`modyfikacjach za pośrednictwem lub .</span><span class="sxs-lookup"><span data-stu-id="5ece5-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5ece5-165">Na przykład `dotnet user-secrets remove "Movies:ConnectionString"` uruchamianie `Movies` zwija dosłowny obiekt.</span><span class="sxs-lookup"><span data-stu-id="5ece5-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5ece5-166">Zmodyfikowany plik przypomina następującą:</span><span class="sxs-lookup"><span data-stu-id="5ece5-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5ece5-167">Ustawianie wielu wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="5ece5-167">Set multiple secrets</span></span>

<span data-ttu-id="5ece5-168">Partia wpisów tajnych można ustawić przez `set` rurociągów JSON do polecenia.</span><span class="sxs-lookup"><span data-stu-id="5ece5-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5ece5-169">W poniższym przykładzie zawartość pliku *input.json* są `set` potokami do polecenia.</span><span class="sxs-lookup"><span data-stu-id="5ece5-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5ece5-170">Windows</span><span class="sxs-lookup"><span data-stu-id="5ece5-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="5ece5-171">Otwórz powłokę polecenia i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5ece5-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5ece5-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5ece5-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5ece5-173">Otwórz powłokę polecenia i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5ece5-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5ece5-174">Dostęp do tajemnicy</span><span class="sxs-lookup"><span data-stu-id="5ece5-174">Access a secret</span></span>

<span data-ttu-id="5ece5-175">[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do tajnych wpisów tajnych menedżera.</span><span class="sxs-lookup"><span data-stu-id="5ece5-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5ece5-176">W ASP.NET Core 2.0 lub nowszym źródło konfiguracji wpisów tajnych użytkowników <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> jest automatycznie dodawane w trybie rozwoju, gdy projekt wywołuje zainicjowanie nowego wystąpienia hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="5ece5-176">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5ece5-177">`CreateDefaultBuilder`połączenia, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> gdy <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>jest:</span><span class="sxs-lookup"><span data-stu-id="5ece5-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="5ece5-178">Gdy `CreateDefaultBuilder` nie jest wywoływana, dodaj źródło konfiguracji <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wpisów `Startup` tajnych użytkownika jawnie wywołując w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="5ece5-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="5ece5-179">Wywołaj `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5ece5-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="5ece5-180">Wpisy tajne użytkownika można `Configuration` pobrać za pośrednictwem interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="5ece5-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5ece5-181">Mapowanie wpisów tajnych do poco</span><span class="sxs-lookup"><span data-stu-id="5ece5-181">Map secrets to a POCO</span></span>

<span data-ttu-id="5ece5-182">Mapowanie całego obiektu literału do POCO (prosta klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="5ece5-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-183">Aby zamapować poprzednie wpisy tajne `Configuration` do poco, należy użyć funkcji [powiązania wykresu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5ece5-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5ece5-184">Następujący kod wiąże się `MovieSettings` z niestandardowym poco i uzyskuje dostęp do wartości `ServiceApiKey` właściwości:</span><span class="sxs-lookup"><span data-stu-id="5ece5-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5ece5-185">`Movies:ConnectionString` Wpisy `Movies:ServiceApiKey` tajne są mapowane do `MovieSettings`odpowiednich właściwości w:</span><span class="sxs-lookup"><span data-stu-id="5ece5-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5ece5-186">Wymiana ciągów z wpisami tajnymi</span><span class="sxs-lookup"><span data-stu-id="5ece5-186">String replacement with secrets</span></span>

<span data-ttu-id="5ece5-187">Przechowywanie haseł w postaci zwykłego tekstu jest niezabezpieczone.</span><span class="sxs-lookup"><span data-stu-id="5ece5-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5ece5-188">Na przykład parametry połączenia bazy danych przechowywane w *appsettings.json* może zawierać hasło dla określonego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="5ece5-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5ece5-189">Bezpieczniejszym podejściem jest przechowywanie hasła jako klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="5ece5-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5ece5-190">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5ece5-191">Usuń `Password` parę klucz-wartość z ciągu połączenia w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5ece5-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5ece5-192">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-192">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5ece5-193">Wartość klucza tajnego można <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ustawić na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> właściwość obiektu, aby zakończyć parametry połączenia:</span><span class="sxs-lookup"><span data-stu-id="5ece5-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5ece5-194">Lista wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="5ece5-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-195">Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5ece5-196">Zostaną wyświetlone następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="5ece5-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5ece5-197">W poprzednim przykładzie dwukropek w nazwach głównych oznacza hierarchię obiektów w pliku *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="5ece5-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5ece5-198">Usuwanie pojedynczego klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="5ece5-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-199">Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5ece5-200">Plik *secrets.json* aplikacji został zmodyfikowany w celu usunięcia pary klucz-wartość skojarzonej z kluczem: `MoviesConnectionString`</span><span class="sxs-lookup"><span data-stu-id="5ece5-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5ece5-201">`dotnet user-secrets list`wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="5ece5-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5ece5-202">Usuń wszystkie wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="5ece5-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-203">Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5ece5-204">Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z pliku *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5ece5-205">Uruchomiony `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="5ece5-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5ece5-206">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5ece5-206">Additional resources</span></span>

* <span data-ttu-id="5ece5-207">Zobacz [ten problem,](https://github.com/dotnet/AspNetCore.Docs/issues/16328) aby uzyskać informacje na temat uzyskiwania dostępu do tajnego menedżera z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5ece5-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5ece5-208">Rick [Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)i Scott [Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5ece5-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5ece5-209">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5ece5-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5ece5-210">W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="5ece5-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5ece5-211">Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie źródłowym.</span><span class="sxs-lookup"><span data-stu-id="5ece5-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5ece5-212">Wpisy tajne produkcji nie powinny być używane do programowania lub testowania.</span><span class="sxs-lookup"><span data-stu-id="5ece5-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5ece5-213">Wpisy tajne nie powinny być wdrażane z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="5ece5-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5ece5-214">Zamiast tego wpisy tajne powinny być udostępniane w środowisku produkcyjnym za pomocą kontrolowanych środków, takich jak zmienne środowiskowe, usługa Azure Key Vault itp. Wpisy tajne testów i produkcji platformy Azure można przechowywać i chronić za pomocą [dostawcy konfiguracji usługi Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="5ece5-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5ece5-215">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="5ece5-215">Environment variables</span></span>

<span data-ttu-id="5ece5-216">Zmienne środowiskowe są używane w celu uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="5ece5-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5ece5-217">Zmienne środowiskowe zastępują wartości konfiguracji dla wszystkich wcześniej określonych źródeł konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5ece5-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5ece5-218">Należy wziąć pod uwagę ASP.NET podstawowej aplikacji sieci web, w której zabezpieczenia **poszczególnych kont użytkowników** jest włączona.</span><span class="sxs-lookup"><span data-stu-id="5ece5-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5ece5-219">Domyślny ciąg połączenia bazy danych znajduje się w pliku *appsettings.json* projektu z kluczem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="5ece5-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5ece5-220">Domyślny ciąg połączenia jest dla LocalDB, który działa w trybie użytkownika i nie wymaga hasła.</span><span class="sxs-lookup"><span data-stu-id="5ece5-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5ece5-221">Podczas wdrażania aplikacji `DefaultConnection` wartość klucza można zastąpić wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="5ece5-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5ece5-222">Zmienna środowiskowa może przechowywać pełny ciąg połączenia z poufnymi poświadczeniami.</span><span class="sxs-lookup"><span data-stu-id="5ece5-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5ece5-223">Zmienne środowiskowe są zazwyczaj przechowywane w zwykłym, niezaszyfrowanym tekście.</span><span class="sxs-lookup"><span data-stu-id="5ece5-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5ece5-224">Jeśli maszyna lub proces zostanie naruszona, zmienne środowiskowe są dostępne dla niezaufanych stron.</span><span class="sxs-lookup"><span data-stu-id="5ece5-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5ece5-225">Mogą być wymagane dodatkowe środki zapobiegające ujawnieniu informacji o tajemnicach użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5ece5-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5ece5-226">Tajny menedżer</span><span class="sxs-lookup"><span data-stu-id="5ece5-226">Secret Manager</span></span>

<span data-ttu-id="5ece5-227">Narzędzie Secret Manager przechowuje poufne dane podczas opracowywania projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5ece5-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5ece5-228">W tym kontekście fragment poufnych danych jest kluczem tajnym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5ece5-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5ece5-229">Wpisy tajne aplikacji są przechowywane w osobnej lokalizacji od drzewa projektu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5ece5-230">Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w kilku projektach.</span><span class="sxs-lookup"><span data-stu-id="5ece5-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5ece5-231">Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="5ece5-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5ece5-232">Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinno być traktowane jako zaufany magazyn.</span><span class="sxs-lookup"><span data-stu-id="5ece5-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5ece5-233">To tylko do celów rozwojowych.</span><span class="sxs-lookup"><span data-stu-id="5ece5-233">It's for development purposes only.</span></span> <span data-ttu-id="5ece5-234">Klucze i wartości są przechowywane w pliku konfiguracyjnym JSON w katalogu profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5ece5-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5ece5-235">Jak działa narzędzie Secret Manager</span><span class="sxs-lookup"><span data-stu-id="5ece5-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="5ece5-236">Narzędzie Secret Manager odsuń szczegóły implementacji, takie jak gdzie i jak wartości są przechowywane.</span><span class="sxs-lookup"><span data-stu-id="5ece5-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5ece5-237">Można użyć narzędzia bez znajomości tych szczegółów implementacji.</span><span class="sxs-lookup"><span data-stu-id="5ece5-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5ece5-238">Wartości są przechowywane w pliku konfiguracyjnym JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:</span><span class="sxs-lookup"><span data-stu-id="5ece5-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5ece5-239">Windows</span><span class="sxs-lookup"><span data-stu-id="5ece5-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="5ece5-240">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="5ece5-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5ece5-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5ece5-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5ece5-242">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="5ece5-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5ece5-243">W poprzednich ścieżkach plików `<user_secrets_id>` zastąp wartością `UserSecretsId` określoną w pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5ece5-244">Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisanych za pomocą narzędzia Menedżer tajny.</span><span class="sxs-lookup"><span data-stu-id="5ece5-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5ece5-245">Te szczegóły implementacji mogą ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="5ece5-245">These implementation details may change.</span></span> <span data-ttu-id="5ece5-246">Na przykład wartości tajne nie są szyfrowane, ale może być w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="5ece5-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5ece5-247">Włączanie pamięci masowej tajnej</span><span class="sxs-lookup"><span data-stu-id="5ece5-247">Enable secret storage</span></span>

<span data-ttu-id="5ece5-248">Narzędzie Secret Manager działa na ustawieniach konfiguracji specyficznych dla projektu przechowywanych w profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5ece5-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5ece5-249">Aby użyć wpisów `UserSecretsId` tajnych `PropertyGroup` użytkownika, zdefiniuj element w pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5ece5-250">Wewnętrzny tekst `UserSecretsId` jest dowolny, ale jest unikatowy dla projektu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="5ece5-251">Deweloperzy zazwyczaj generują identyfikator `UserSecretsId`GUID dla pliku .</span><span class="sxs-lookup"><span data-stu-id="5ece5-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="5ece5-252">W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **Zarządzaj wpisami tajnymi użytkowników** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="5ece5-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5ece5-253">Ten gest `UserSecretsId` dodaje element wypełniony identyfikatorem GUID do pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5ece5-254">Ustawianie klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="5ece5-254">Set a secret</span></span>

<span data-ttu-id="5ece5-255">Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości.</span><span class="sxs-lookup"><span data-stu-id="5ece5-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5ece5-256">Klucz tajny jest skojarzony z `UserSecretsId` wartością projektu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5ece5-257">Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5ece5-258">W poprzednim przykładzie dwukropek oznacza, że `Movies` jest obiektem dosłownym z właściwością. `ServiceApiKey`</span><span class="sxs-lookup"><span data-stu-id="5ece5-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5ece5-259">Narzędzie Secret Manager może być również używane z innych katalogów.</span><span class="sxs-lookup"><span data-stu-id="5ece5-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5ece5-260">Użyj `--project` tej opcji, aby podać ścieżkę systemu plików, przy której istnieje plik *csproj.*</span><span class="sxs-lookup"><span data-stu-id="5ece5-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5ece5-261">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5ece5-262">Spłaszczanie struktury JSON w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5ece5-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5ece5-263">Gest Zarządzanie **wpisami tajnymi użytkowników** w programie Visual Studio otwiera plik *secrets.json* w edytorze tekstu.</span><span class="sxs-lookup"><span data-stu-id="5ece5-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5ece5-264">Zastąp zawartość *pliku secrets.json* parami klucz-wartość, które mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="5ece5-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5ece5-265">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5ece5-266">Struktura JSON jest spłaszczana po `dotnet user-secrets remove` `dotnet user-secrets set`modyfikacjach za pośrednictwem lub .</span><span class="sxs-lookup"><span data-stu-id="5ece5-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5ece5-267">Na przykład `dotnet user-secrets remove "Movies:ConnectionString"` uruchamianie `Movies` zwija dosłowny obiekt.</span><span class="sxs-lookup"><span data-stu-id="5ece5-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5ece5-268">Zmodyfikowany plik przypomina następującą:</span><span class="sxs-lookup"><span data-stu-id="5ece5-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5ece5-269">Ustawianie wielu wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="5ece5-269">Set multiple secrets</span></span>

<span data-ttu-id="5ece5-270">Partia wpisów tajnych można ustawić przez `set` rurociągów JSON do polecenia.</span><span class="sxs-lookup"><span data-stu-id="5ece5-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5ece5-271">W poniższym przykładzie zawartość pliku *input.json* są `set` potokami do polecenia.</span><span class="sxs-lookup"><span data-stu-id="5ece5-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5ece5-272">Windows</span><span class="sxs-lookup"><span data-stu-id="5ece5-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="5ece5-273">Otwórz powłokę polecenia i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5ece5-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5ece5-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5ece5-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5ece5-275">Otwórz powłokę polecenia i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5ece5-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5ece5-276">Dostęp do tajemnicy</span><span class="sxs-lookup"><span data-stu-id="5ece5-276">Access a secret</span></span>

<span data-ttu-id="5ece5-277">[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do tajnych wpisów tajnych menedżera.</span><span class="sxs-lookup"><span data-stu-id="5ece5-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5ece5-278">Jeśli projekt jest przeznaczony dla platformy .NET Framework, zainstaluj pakiet [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="5ece5-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>


<span data-ttu-id="5ece5-279">W ASP.NET Core 2.0 lub nowszym źródło konfiguracji wpisów tajnych użytkowników <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> jest automatycznie dodawane w trybie rozwoju, gdy projekt wywołuje zainicjowanie nowego wystąpienia hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="5ece5-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5ece5-280">`CreateDefaultBuilder`połączenia, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> gdy <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>jest:</span><span class="sxs-lookup"><span data-stu-id="5ece5-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


<span data-ttu-id="5ece5-281">Gdy `CreateDefaultBuilder` nie jest wywoływana, dodaj źródło konfiguracji <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wpisów `Startup` tajnych użytkownika jawnie wywołując w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="5ece5-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="5ece5-282">Wywołaj `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5ece5-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="5ece5-283">Wpisy tajne użytkownika można `Configuration` pobrać za pośrednictwem interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="5ece5-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5ece5-284">Mapowanie wpisów tajnych do poco</span><span class="sxs-lookup"><span data-stu-id="5ece5-284">Map secrets to a POCO</span></span>

<span data-ttu-id="5ece5-285">Mapowanie całego obiektu literału do POCO (prosta klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="5ece5-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-286">Aby zamapować poprzednie wpisy tajne `Configuration` do poco, należy użyć funkcji [powiązania wykresu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5ece5-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5ece5-287">Następujący kod wiąże się `MovieSettings` z niestandardowym poco i uzyskuje dostęp do wartości `ServiceApiKey` właściwości:</span><span class="sxs-lookup"><span data-stu-id="5ece5-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5ece5-288">`Movies:ConnectionString` Wpisy `Movies:ServiceApiKey` tajne są mapowane do `MovieSettings`odpowiednich właściwości w:</span><span class="sxs-lookup"><span data-stu-id="5ece5-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5ece5-289">Wymiana ciągów z wpisami tajnymi</span><span class="sxs-lookup"><span data-stu-id="5ece5-289">String replacement with secrets</span></span>

<span data-ttu-id="5ece5-290">Przechowywanie haseł w postaci zwykłego tekstu jest niezabezpieczone.</span><span class="sxs-lookup"><span data-stu-id="5ece5-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5ece5-291">Na przykład parametry połączenia bazy danych przechowywane w *appsettings.json* może zawierać hasło dla określonego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="5ece5-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5ece5-292">Bezpieczniejszym podejściem jest przechowywanie hasła jako klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="5ece5-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5ece5-293">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5ece5-294">Usuń `Password` parę klucz-wartość z ciągu połączenia w *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5ece5-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5ece5-295">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5ece5-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5ece5-296">Wartość klucza tajnego można <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ustawić na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> właściwość obiektu, aby zakończyć parametry połączenia:</span><span class="sxs-lookup"><span data-stu-id="5ece5-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5ece5-297">Lista wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="5ece5-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-298">Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5ece5-299">Zostaną wyświetlone następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="5ece5-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5ece5-300">W poprzednim przykładzie dwukropek w nazwach głównych oznacza hierarchię obiektów w pliku *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="5ece5-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5ece5-301">Usuwanie pojedynczego klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="5ece5-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-302">Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5ece5-303">Plik *secrets.json* aplikacji został zmodyfikowany w celu usunięcia pary klucz-wartość skojarzonej z kluczem: `MoviesConnectionString`</span><span class="sxs-lookup"><span data-stu-id="5ece5-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5ece5-304">Uruchomiony `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="5ece5-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5ece5-305">Usuń wszystkie wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="5ece5-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5ece5-306">Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5ece5-307">Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z pliku *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="5ece5-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5ece5-308">Uruchomiony `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="5ece5-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5ece5-309">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5ece5-309">Additional resources</span></span>

* <span data-ttu-id="5ece5-310">Zobacz [ten problem,](https://github.com/dotnet/AspNetCore.Docs/issues/16328) aby uzyskać informacje na temat uzyskiwania dostępu do tajnego menedżera z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5ece5-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end