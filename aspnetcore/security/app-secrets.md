---
title: Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak przechowywać i pobierać poufne informacje jako wpisy tajne aplikacji podczas opracowywania aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056871"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="1084b-103">Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1084b-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1084b-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="1084b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="1084b-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1084b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1084b-106">W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="1084b-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="1084b-107">Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym.</span><span class="sxs-lookup"><span data-stu-id="1084b-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="1084b-108">Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="1084b-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="1084b-109">Wpisy tajne nie powinny być wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1084b-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="1084b-110">Zamiast tego należy udostępnić wpisy tajne w środowisku produkcyjnym za pomocą kontrolowanych środków, takich jak zmienne środowiskowe, Azure Key Vault itd. Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="1084b-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="1084b-111">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="1084b-111">Environment variables</span></span>

<span data-ttu-id="1084b-112">Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1084b-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="1084b-113">Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1084b-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="1084b-114">Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** .</span><span class="sxs-lookup"><span data-stu-id="1084b-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="1084b-115">Domyślne parametry połączenia z bazą danych są zawarte w pliku projektu *appsettings.json* z kluczem `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="1084b-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="1084b-116">Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła.</span><span class="sxs-lookup"><span data-stu-id="1084b-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="1084b-117">Podczas wdrażania aplikacji `DefaultConnection` wartość klucza może być zastąpiona wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="1084b-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="1084b-118">Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.</span><span class="sxs-lookup"><span data-stu-id="1084b-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="1084b-119">Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu.</span><span class="sxs-lookup"><span data-stu-id="1084b-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="1084b-120">W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron.</span><span class="sxs-lookup"><span data-stu-id="1084b-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="1084b-121">Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1084b-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="1084b-122">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="1084b-122">Secret Manager</span></span>

<span data-ttu-id="1084b-123">Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1084b-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="1084b-124">W tym kontekście dane poufne są tajne dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1084b-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="1084b-125">Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu.</span><span class="sxs-lookup"><span data-stu-id="1084b-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="1084b-126">Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="1084b-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="1084b-127">Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="1084b-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="1084b-128">Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn.</span><span class="sxs-lookup"><span data-stu-id="1084b-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="1084b-129">Jest przeznaczona tylko do celów programistycznych.</span><span class="sxs-lookup"><span data-stu-id="1084b-129">It's for development purposes only.</span></span> <span data-ttu-id="1084b-130">Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1084b-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="1084b-131">Jak działa narzędzie do zarządzania kluczami tajnymi</span><span class="sxs-lookup"><span data-stu-id="1084b-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="1084b-132">Narzędzie tajnego Menedżera wyodrębnia szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości.</span><span class="sxs-lookup"><span data-stu-id="1084b-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="1084b-133">Możesz użyć narzędzia bez znajomości tych szczegółów implementacji.</span><span class="sxs-lookup"><span data-stu-id="1084b-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="1084b-134">Wartości są przechowywane w pliku konfiguracji JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:</span><span class="sxs-lookup"><span data-stu-id="1084b-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="1084b-135">Windows</span><span class="sxs-lookup"><span data-stu-id="1084b-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="1084b-136">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="1084b-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="1084b-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="1084b-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1084b-138">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="1084b-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="1084b-139">W poprzednich ścieżkach plików Zamień na `<user_secrets_id>` `UserSecretsId` wartość określoną w pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="1084b-140">Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi.</span><span class="sxs-lookup"><span data-stu-id="1084b-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="1084b-141">Te szczegóły implementacji mogą ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="1084b-141">These implementation details may change.</span></span> <span data-ttu-id="1084b-142">Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="1084b-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="1084b-143">Włącz magazyn tajny</span><span class="sxs-lookup"><span data-stu-id="1084b-143">Enable secret storage</span></span>

<span data-ttu-id="1084b-144">Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1084b-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="1084b-145">Narzędzie Secret Manager zawiera `init` polecenie w zestaw .NET Core SDK 3.0.100 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="1084b-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="1084b-146">Aby użyć kluczy tajnych użytkownika, uruchom następujące polecenie w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="1084b-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="1084b-147">Poprzednie polecenie dodaje `UserSecretsId` element w `PropertyGroup` pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="1084b-148">Domyślnie tekst wewnętrzny `UserSecretsId` jest identyfikatorem GUID.</span><span class="sxs-lookup"><span data-stu-id="1084b-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="1084b-149">Wewnętrzny tekst jest dowolny, ale jest unikatowy dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1084b-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="1084b-150">W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="1084b-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="1084b-151">Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="1084b-152">Ustaw klucz tajny</span><span class="sxs-lookup"><span data-stu-id="1084b-152">Set a secret</span></span>

<span data-ttu-id="1084b-153">Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości.</span><span class="sxs-lookup"><span data-stu-id="1084b-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="1084b-154">Wpis tajny jest skojarzony z wartością projektu `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="1084b-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="1084b-155">Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="1084b-156">W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu z `ServiceApiKey` właściwością.</span><span class="sxs-lookup"><span data-stu-id="1084b-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="1084b-157">Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów.</span><span class="sxs-lookup"><span data-stu-id="1084b-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="1084b-158">Użyj `--project` opcji, aby podać ścieżkę systemu plików, w której znajduje się plik *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="1084b-159">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="1084b-160">Spłaszczanie struktury JSON w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1084b-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="1084b-161">Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera *secrets.js* w pliku w edytorze tekstów.</span><span class="sxs-lookup"><span data-stu-id="1084b-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="1084b-162">Zastąp zawartość *secrets.js* przy użyciu par klucz-wartość, które mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="1084b-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="1084b-163">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1084b-164">Struktura JSON jest spłaszczona po modyfikacji za pośrednictwem `dotnet user-secrets remove` lub `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="1084b-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="1084b-165">Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="1084b-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="1084b-166">Zmodyfikowany plik jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="1084b-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="1084b-167">Ustawianie wielu wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="1084b-167">Set multiple secrets</span></span>

<span data-ttu-id="1084b-168">Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="1084b-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="1084b-169">W poniższym przykładzie *input.js* zawartości pliku są potoku do `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="1084b-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="1084b-170">Windows</span><span class="sxs-lookup"><span data-stu-id="1084b-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="1084b-171">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1084b-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="1084b-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="1084b-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1084b-173">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1084b-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="1084b-174">Dostęp do klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="1084b-174">Access a secret</span></span>

<span data-ttu-id="1084b-175">[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do wpisów tajnych usługi Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="1084b-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="1084b-176">Źródło konfiguracji kluczy tajnych użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt wywoła <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> nowe wystąpienie hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="1084b-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="1084b-177">`CreateDefaultBuilder` wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> się, gdy <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> jest <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="1084b-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="1084b-178">Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie przez wywołanie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="1084b-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="1084b-179">Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1084b-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="1084b-180">Wpisy tajne użytkownika można pobrać za pośrednictwem `Configuration` interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="1084b-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="1084b-181">Mapuj wpisy tajne do POCO</span><span class="sxs-lookup"><span data-stu-id="1084b-181">Map secrets to a POCO</span></span>

<span data-ttu-id="1084b-182">Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="1084b-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-183">Aby zmapować poprzednie wpisy tajne do POCO, użyj `Configuration` funkcji [powiązania grafu obiektów](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1084b-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="1084b-184">Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp do `ServiceApiKey` wartości właściwości:</span><span class="sxs-lookup"><span data-stu-id="1084b-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="1084b-185">Wpisy `Movies:ConnectionString` `Movies:ServiceApiKey` tajne i są mapowane na odpowiednie właściwości w programie `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="1084b-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="1084b-186">Zastępowanie ciągów hasłami tajnymi</span><span class="sxs-lookup"><span data-stu-id="1084b-186">String replacement with secrets</span></span>

<span data-ttu-id="1084b-187">Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="1084b-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="1084b-188">Na przykład parametry połączenia z bazą danych przechowywane w programie *appsettings.json* mogą zawierać hasło dla określonego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1084b-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="1084b-189">Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="1084b-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="1084b-190">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="1084b-191">Usuń `Password` parę klucz-wartość z parametrów połączenia w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1084b-191">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="1084b-192">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="1084b-193">Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> właściwości obiektu, <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Aby zakończyć parametry połączenia:</span><span class="sxs-lookup"><span data-stu-id="1084b-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="1084b-194">Wystaw wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="1084b-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-195">Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="1084b-196">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="1084b-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="1084b-197">W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w *secrets.jsna* .</span><span class="sxs-lookup"><span data-stu-id="1084b-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="1084b-198">Usuń pojedynczy klucz tajny</span><span class="sxs-lookup"><span data-stu-id="1084b-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-199">Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="1084b-200">*secrets.jsaplikacji w* pliku został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:</span><span class="sxs-lookup"><span data-stu-id="1084b-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1084b-201">`dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="1084b-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="1084b-202">Usuń wszystkie wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="1084b-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-203">Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="1084b-204">Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z *secrets.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="1084b-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="1084b-205">Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="1084b-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="1084b-206">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1084b-206">Additional resources</span></span>

* <span data-ttu-id="1084b-207">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16328) , aby uzyskać informacje na temat uzyskiwania dostępu do Menedżera kluczy tajnych z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1084b-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1084b-208">[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="1084b-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="1084b-209">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1084b-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1084b-210">W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="1084b-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="1084b-211">Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym.</span><span class="sxs-lookup"><span data-stu-id="1084b-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="1084b-212">Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="1084b-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="1084b-213">Wpisy tajne nie powinny być wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1084b-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="1084b-214">Zamiast tego należy udostępnić wpisy tajne w środowisku produkcyjnym za pomocą kontrolowanych środków, takich jak zmienne środowiskowe, Azure Key Vault itd. Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="1084b-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="1084b-215">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="1084b-215">Environment variables</span></span>

<span data-ttu-id="1084b-216">Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1084b-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="1084b-217">Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1084b-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="1084b-218">Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** .</span><span class="sxs-lookup"><span data-stu-id="1084b-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="1084b-219">Domyślne parametry połączenia z bazą danych są zawarte w pliku projektu *appsettings.json* z kluczem `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="1084b-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="1084b-220">Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła.</span><span class="sxs-lookup"><span data-stu-id="1084b-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="1084b-221">Podczas wdrażania aplikacji `DefaultConnection` wartość klucza może być zastąpiona wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="1084b-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="1084b-222">Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.</span><span class="sxs-lookup"><span data-stu-id="1084b-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="1084b-223">Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu.</span><span class="sxs-lookup"><span data-stu-id="1084b-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="1084b-224">W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron.</span><span class="sxs-lookup"><span data-stu-id="1084b-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="1084b-225">Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1084b-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="1084b-226">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="1084b-226">Secret Manager</span></span>

<span data-ttu-id="1084b-227">Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1084b-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="1084b-228">W tym kontekście dane poufne są tajne dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1084b-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="1084b-229">Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu.</span><span class="sxs-lookup"><span data-stu-id="1084b-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="1084b-230">Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="1084b-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="1084b-231">Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="1084b-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="1084b-232">Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn.</span><span class="sxs-lookup"><span data-stu-id="1084b-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="1084b-233">Jest przeznaczona tylko do celów programistycznych.</span><span class="sxs-lookup"><span data-stu-id="1084b-233">It's for development purposes only.</span></span> <span data-ttu-id="1084b-234">Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1084b-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="1084b-235">Jak działa narzędzie do zarządzania kluczami tajnymi</span><span class="sxs-lookup"><span data-stu-id="1084b-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="1084b-236">Narzędzie tajnego Menedżera wyodrębnia szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości.</span><span class="sxs-lookup"><span data-stu-id="1084b-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="1084b-237">Możesz użyć narzędzia bez znajomości tych szczegółów implementacji.</span><span class="sxs-lookup"><span data-stu-id="1084b-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="1084b-238">Wartości są przechowywane w pliku konfiguracji JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:</span><span class="sxs-lookup"><span data-stu-id="1084b-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="1084b-239">Windows</span><span class="sxs-lookup"><span data-stu-id="1084b-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="1084b-240">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="1084b-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="1084b-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="1084b-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1084b-242">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="1084b-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="1084b-243">W poprzednich ścieżkach plików Zamień na `<user_secrets_id>` `UserSecretsId` wartość określoną w pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="1084b-244">Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi.</span><span class="sxs-lookup"><span data-stu-id="1084b-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="1084b-245">Te szczegóły implementacji mogą ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="1084b-245">These implementation details may change.</span></span> <span data-ttu-id="1084b-246">Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="1084b-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="1084b-247">Włącz magazyn tajny</span><span class="sxs-lookup"><span data-stu-id="1084b-247">Enable secret storage</span></span>

<span data-ttu-id="1084b-248">Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1084b-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="1084b-249">Aby użyć kluczy tajnych użytkownika, zdefiniuj `UserSecretsId` element w `PropertyGroup` pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="1084b-250">Wewnętrzny tekst `UserSecretsId` jest dowolny, ale jest unikatowy dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1084b-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="1084b-251">Deweloperzy zwykle generują identyfikator GUID dla `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="1084b-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="1084b-252">W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="1084b-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="1084b-253">Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="1084b-254">Ustaw klucz tajny</span><span class="sxs-lookup"><span data-stu-id="1084b-254">Set a secret</span></span>

<span data-ttu-id="1084b-255">Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości.</span><span class="sxs-lookup"><span data-stu-id="1084b-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="1084b-256">Wpis tajny jest skojarzony z wartością projektu `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="1084b-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="1084b-257">Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="1084b-258">W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu z `ServiceApiKey` właściwością.</span><span class="sxs-lookup"><span data-stu-id="1084b-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="1084b-259">Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów.</span><span class="sxs-lookup"><span data-stu-id="1084b-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="1084b-260">Użyj `--project` opcji, aby podać ścieżkę systemu plików, w której znajduje się plik *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="1084b-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="1084b-261">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="1084b-262">Spłaszczanie struktury JSON w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1084b-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="1084b-263">Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera *secrets.js* w pliku w edytorze tekstów.</span><span class="sxs-lookup"><span data-stu-id="1084b-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="1084b-264">Zastąp zawartość *secrets.js* przy użyciu par klucz-wartość, które mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="1084b-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="1084b-265">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1084b-266">Struktura JSON jest spłaszczona po modyfikacji za pośrednictwem `dotnet user-secrets remove` lub `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="1084b-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="1084b-267">Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="1084b-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="1084b-268">Zmodyfikowany plik jest podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="1084b-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="1084b-269">Ustawianie wielu wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="1084b-269">Set multiple secrets</span></span>

<span data-ttu-id="1084b-270">Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="1084b-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="1084b-271">W poniższym przykładzie *input.js* zawartości pliku są potoku do `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="1084b-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="1084b-272">Windows</span><span class="sxs-lookup"><span data-stu-id="1084b-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="1084b-273">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1084b-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="1084b-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="1084b-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1084b-275">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1084b-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="1084b-276">Dostęp do klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="1084b-276">Access a secret</span></span>

<span data-ttu-id="1084b-277">[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do wpisów tajnych usługi Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="1084b-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="1084b-278">Jeśli projekt jest przeznaczony .NET Framework, zainstaluj [Microsoft.Extensions.Configwersja. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Pakiet NuGet UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="1084b-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="1084b-279">W ASP.NET Core 2,0 lub nowszej Źródło konfiguracji użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt wywoła <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> nowe wystąpienie hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="1084b-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="1084b-280">`CreateDefaultBuilder` wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> się, gdy <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> jest <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="1084b-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="1084b-281">Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie, wywołując <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> w `Startup` konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="1084b-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="1084b-282">Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1084b-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="1084b-283">Wpisy tajne użytkownika można pobrać za pośrednictwem `Configuration` interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="1084b-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="1084b-284">Mapuj wpisy tajne do POCO</span><span class="sxs-lookup"><span data-stu-id="1084b-284">Map secrets to a POCO</span></span>

<span data-ttu-id="1084b-285">Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="1084b-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-286">Aby zmapować poprzednie wpisy tajne do POCO, użyj `Configuration` funkcji [powiązania grafu obiektów](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1084b-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="1084b-287">Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp do `ServiceApiKey` wartości właściwości:</span><span class="sxs-lookup"><span data-stu-id="1084b-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="1084b-288">Wpisy `Movies:ConnectionString` `Movies:ServiceApiKey` tajne i są mapowane na odpowiednie właściwości w programie `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="1084b-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="1084b-289">Zastępowanie ciągów hasłami tajnymi</span><span class="sxs-lookup"><span data-stu-id="1084b-289">String replacement with secrets</span></span>

<span data-ttu-id="1084b-290">Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="1084b-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="1084b-291">Na przykład parametry połączenia z bazą danych przechowywane w programie *appsettings.json* mogą zawierać hasło dla określonego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1084b-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="1084b-292">Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="1084b-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="1084b-293">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="1084b-294">Usuń `Password` parę klucz-wartość z parametrów połączenia w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="1084b-294">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="1084b-295">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1084b-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="1084b-296">Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> właściwości obiektu, <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Aby zakończyć parametry połączenia:</span><span class="sxs-lookup"><span data-stu-id="1084b-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="1084b-297">Wystaw wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="1084b-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-298">Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="1084b-299">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="1084b-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="1084b-300">W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w *secrets.jsna* .</span><span class="sxs-lookup"><span data-stu-id="1084b-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="1084b-301">Usuń pojedynczy klucz tajny</span><span class="sxs-lookup"><span data-stu-id="1084b-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-302">Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="1084b-303">*secrets.jsaplikacji w* pliku został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:</span><span class="sxs-lookup"><span data-stu-id="1084b-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1084b-304">Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="1084b-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="1084b-305">Usuń wszystkie wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="1084b-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1084b-306">Uruchom następujące polecenie z katalogu, w którym istnieje plik *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="1084b-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="1084b-307">Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z *secrets.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="1084b-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="1084b-308">Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="1084b-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="1084b-309">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1084b-309">Additional resources</span></span>

* <span data-ttu-id="1084b-310">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16328) , aby uzyskać informacje na temat uzyskiwania dostępu do Menedżera kluczy tajnych z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="1084b-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end