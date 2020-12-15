---
title: Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak przechowywać i pobierać poufne informacje podczas opracowywania aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
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
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486203"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="dfe04-103">Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dfe04-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dfe04-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="dfe04-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="dfe04-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dfe04-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dfe04-106">W tym dokumencie wyjaśniono, jak zarządzać danymi poufnymi dla aplikacji ASP.NET Core na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dfe04-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="dfe04-107">Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym.</span><span class="sxs-lookup"><span data-stu-id="dfe04-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="dfe04-108">Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="dfe04-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="dfe04-109">Wpisy tajne nie powinny być wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="dfe04-110">Zamiast tego należy uzyskać dostęp do wpisów tajnych produkcji przy użyciu kontrolowanych środków, takich jak zmienne środowiskowe lub Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="dfe04-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="dfe04-111">Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="dfe04-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="dfe04-112">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dfe04-112">Environment variables</span></span>

<span data-ttu-id="dfe04-113">Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="dfe04-114">Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="dfe04-115">Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** .</span><span class="sxs-lookup"><span data-stu-id="dfe04-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="dfe04-116">Domyślne parametry połączenia z bazą danych są zawarte w pliku projektu *appsettings.json* z kluczem `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="dfe04-117">Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła.</span><span class="sxs-lookup"><span data-stu-id="dfe04-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="dfe04-118">Podczas wdrażania aplikacji `DefaultConnection` wartość klucza może być zastąpiona wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="dfe04-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="dfe04-119">Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.</span><span class="sxs-lookup"><span data-stu-id="dfe04-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="dfe04-120">Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="dfe04-121">W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron.</span><span class="sxs-lookup"><span data-stu-id="dfe04-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="dfe04-122">Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="dfe04-123">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="dfe04-123">Secret Manager</span></span>

<span data-ttu-id="dfe04-124">Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dfe04-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="dfe04-125">W tym kontekście dane poufne są tajne dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="dfe04-126">Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="dfe04-127">Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="dfe04-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="dfe04-128">Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="dfe04-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="dfe04-129">Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn.</span><span class="sxs-lookup"><span data-stu-id="dfe04-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="dfe04-130">Jest przeznaczona tylko do celów programistycznych.</span><span class="sxs-lookup"><span data-stu-id="dfe04-130">It's for development purposes only.</span></span> <span data-ttu-id="dfe04-131">Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="dfe04-132">Jak działa narzędzie do zarządzania kluczami tajnymi</span><span class="sxs-lookup"><span data-stu-id="dfe04-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="dfe04-133">Narzędzie Secret Manager ukrywa szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="dfe04-134">Możesz użyć narzędzia bez znajomości tych szczegółów implementacji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="dfe04-135">Wartości są przechowywane w pliku JSON w folderze profilu użytkownika komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="dfe04-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="dfe04-136">Windows</span><span class="sxs-lookup"><span data-stu-id="dfe04-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="dfe04-137">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="dfe04-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="dfe04-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="dfe04-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="dfe04-139">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="dfe04-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="dfe04-140">W poprzednich ścieżkach plików Zamień na `<user_secrets_id>` `UserSecretsId` wartość określoną w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="dfe04-141">Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi.</span><span class="sxs-lookup"><span data-stu-id="dfe04-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="dfe04-142">Te szczegóły implementacji mogą ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="dfe04-142">These implementation details may change.</span></span> <span data-ttu-id="dfe04-143">Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="dfe04-144">Włącz magazyn tajny</span><span class="sxs-lookup"><span data-stu-id="dfe04-144">Enable secret storage</span></span>

<span data-ttu-id="dfe04-145">Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="dfe04-146">Narzędzie Secret Manager zawiera `init` polecenie w zestaw .NET Core SDK 3.0.100 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="dfe04-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="dfe04-147">Aby użyć kluczy tajnych użytkownika, uruchom następujące polecenie w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="dfe04-148">Poprzednie polecenie dodaje `UserSecretsId` element w `PropertyGroup` pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="dfe04-149">Domyślnie tekst wewnętrzny `UserSecretsId` jest identyfikatorem GUID.</span><span class="sxs-lookup"><span data-stu-id="dfe04-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="dfe04-150">Wewnętrzny tekst jest dowolny, ale jest unikatowy dla projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="dfe04-151">W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="dfe04-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="dfe04-152">Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="dfe04-153">Ustaw klucz tajny</span><span class="sxs-lookup"><span data-stu-id="dfe04-153">Set a secret</span></span>

<span data-ttu-id="dfe04-154">Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="dfe04-155">Wpis tajny jest skojarzony z wartością projektu `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="dfe04-156">Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="dfe04-157">W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu z `ServiceApiKey` właściwością.</span><span class="sxs-lookup"><span data-stu-id="dfe04-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="dfe04-158">Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów.</span><span class="sxs-lookup"><span data-stu-id="dfe04-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="dfe04-159">Użyj `--project` opcji, aby podać ścieżkę systemu plików, w której istnieje plik projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="dfe04-160">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="dfe04-161">Spłaszczanie struktury JSON w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfe04-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="dfe04-162">Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera *secrets.js* w pliku w edytorze tekstów.</span><span class="sxs-lookup"><span data-stu-id="dfe04-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="dfe04-163">Zastąp zawartość *secrets.js* przy użyciu par klucz-wartość, które mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="dfe04-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="dfe04-164">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="dfe04-165">Struktura JSON jest spłaszczona po modyfikacji za pośrednictwem `dotnet user-secrets remove` lub `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="dfe04-166">Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="dfe04-167">Zmodyfikowany plik przypomina następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="dfe04-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="dfe04-168">Ustawianie wielu wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="dfe04-168">Set multiple secrets</span></span>

<span data-ttu-id="dfe04-169">Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="dfe04-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="dfe04-170">W poniższym przykładzie *input.js* zawartości pliku są potoku do `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="dfe04-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="dfe04-171">Windows</span><span class="sxs-lookup"><span data-stu-id="dfe04-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="dfe04-172">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="dfe04-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="dfe04-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="dfe04-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="dfe04-174">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="dfe04-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="dfe04-175">Dostęp do klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="dfe04-175">Access a secret</span></span>

<span data-ttu-id="dfe04-176">Aby uzyskać dostęp do wpisu tajnego, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dfe04-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="dfe04-177">Rejestrowanie źródła konfiguracji kluczy tajnych użytkownika</span><span class="sxs-lookup"><span data-stu-id="dfe04-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="dfe04-178">Odczytywanie wpisu tajnego za pośrednictwem interfejsu API konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dfe04-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="dfe04-179">Rejestrowanie źródła konfiguracji kluczy tajnych użytkownika</span><span class="sxs-lookup"><span data-stu-id="dfe04-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="dfe04-180">[Dostawca konfiguracji](/dotnet/core/extensions/configuration-providers) kluczy tajnych użytkownika rejestruje odpowiednie źródło konfiguracji za pomocą [interfejsu API konfiguracji](xref:fundamentals/configuration/index)platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="dfe04-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="dfe04-181">Źródło konfiguracji kluczy tajnych użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt jest wywoływany <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="dfe04-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="dfe04-182">`CreateDefaultBuilder` wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> się, gdy <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> jest <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="dfe04-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="dfe04-183">Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie przez wywołanie metody <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="dfe04-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="dfe04-184">Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="dfe04-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="dfe04-185">Odczytywanie wpisu tajnego za pośrednictwem interfejsu API konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dfe04-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="dfe04-186">Jeśli źródło konfiguracji kluczy tajnych użytkownika zostanie zarejestrowane, interfejs API konfiguracji platformy .NET może odczytać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="dfe04-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="dfe04-187">[Iniekcja konstruktora](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) może służyć do uzyskania dostępu do interfejsu API konfiguracji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="dfe04-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="dfe04-188">Rozważ następujące przykłady odczytywania `Movies:ServiceApiKey` klucza:</span><span class="sxs-lookup"><span data-stu-id="dfe04-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="dfe04-189">**Klasa początkowa:**</span><span class="sxs-lookup"><span data-stu-id="dfe04-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="dfe04-190">**Razor Model strony stron:**</span><span class="sxs-lookup"><span data-stu-id="dfe04-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="dfe04-191">Aby uzyskać więcej informacji, zobacz [Konfiguracja dostępu w](xref:fundamentals/configuration/index#access-configuration-in-startup) [konfiguracjach uruchamiania i dostępu na Razor stronach](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="dfe04-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="dfe04-192">Mapuj wpisy tajne do POCO</span><span class="sxs-lookup"><span data-stu-id="dfe04-192">Map secrets to a POCO</span></span>

<span data-ttu-id="dfe04-193">Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-194">Aby zmapować poprzednie wpisy tajne do POCO, użyj funkcji [powiązania grafu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API konfiguracji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="dfe04-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="dfe04-195">Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp do `ServiceApiKey` wartości właściwości:</span><span class="sxs-lookup"><span data-stu-id="dfe04-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="dfe04-196">Wpisy `Movies:ConnectionString` `Movies:ServiceApiKey` tajne i są mapowane na odpowiednie właściwości w programie `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="dfe04-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="dfe04-197">Zastępowanie ciągów hasłami tajnymi</span><span class="sxs-lookup"><span data-stu-id="dfe04-197">String replacement with secrets</span></span>

<span data-ttu-id="dfe04-198">Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="dfe04-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="dfe04-199">Na przykład parametry połączenia z bazą danych przechowywane w programie *appsettings.json* mogą zawierać hasło dla określonego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="dfe04-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="dfe04-200">Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="dfe04-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="dfe04-201">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="dfe04-202">Usuń `Password` parę klucz-wartość z parametrów połączenia w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dfe04-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="dfe04-203">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="dfe04-204">Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> właściwości obiektu, <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Aby zakończyć parametry połączenia:</span><span class="sxs-lookup"><span data-stu-id="dfe04-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="dfe04-205">Wystaw wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="dfe04-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-206">Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="dfe04-207">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="dfe04-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="dfe04-208">W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w *secrets.jsna*.</span><span class="sxs-lookup"><span data-stu-id="dfe04-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="dfe04-209">Usuń pojedynczy klucz tajny</span><span class="sxs-lookup"><span data-stu-id="dfe04-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-210">Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="dfe04-211">*secrets.jsaplikacji w* pliku został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:</span><span class="sxs-lookup"><span data-stu-id="dfe04-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="dfe04-212">`dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="dfe04-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="dfe04-213">Usuń wszystkie wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="dfe04-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-214">Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="dfe04-215">Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z *secrets.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="dfe04-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="dfe04-216">Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="dfe04-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="dfe04-217">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="dfe04-217">Additional resources</span></span>

* <span data-ttu-id="dfe04-218">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16328) , aby uzyskać informacje na temat uzyskiwania dostępu do kluczy tajnych użytkownika z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="dfe04-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dfe04-219">[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="dfe04-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="dfe04-220">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dfe04-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dfe04-221">W tym dokumencie wyjaśniono, jak zarządzać danymi poufnymi dla aplikacji ASP.NET Core na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="dfe04-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="dfe04-222">Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym.</span><span class="sxs-lookup"><span data-stu-id="dfe04-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="dfe04-223">Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="dfe04-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="dfe04-224">Wpisy tajne nie powinny być wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="dfe04-225">Zamiast tego należy uzyskać dostęp do wpisów tajnych produkcji przy użyciu kontrolowanych środków, takich jak zmienne środowiskowe lub Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="dfe04-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="dfe04-226">Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="dfe04-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="dfe04-227">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="dfe04-227">Environment variables</span></span>

<span data-ttu-id="dfe04-228">Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="dfe04-229">Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="dfe04-230">Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** .</span><span class="sxs-lookup"><span data-stu-id="dfe04-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="dfe04-231">Domyślne parametry połączenia z bazą danych są zawarte w pliku projektu *appsettings.json* z kluczem `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="dfe04-232">Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła.</span><span class="sxs-lookup"><span data-stu-id="dfe04-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="dfe04-233">Podczas wdrażania aplikacji `DefaultConnection` wartość klucza może być zastąpiona wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="dfe04-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="dfe04-234">Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.</span><span class="sxs-lookup"><span data-stu-id="dfe04-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="dfe04-235">Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="dfe04-236">W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron.</span><span class="sxs-lookup"><span data-stu-id="dfe04-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="dfe04-237">Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="dfe04-238">Menedżer wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="dfe04-238">Secret Manager</span></span>

<span data-ttu-id="dfe04-239">Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dfe04-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="dfe04-240">W tym kontekście dane poufne są tajne dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="dfe04-241">Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="dfe04-242">Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="dfe04-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="dfe04-243">Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="dfe04-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="dfe04-244">Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn.</span><span class="sxs-lookup"><span data-stu-id="dfe04-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="dfe04-245">Jest przeznaczona tylko do celów programistycznych.</span><span class="sxs-lookup"><span data-stu-id="dfe04-245">It's for development purposes only.</span></span> <span data-ttu-id="dfe04-246">Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="dfe04-247">Jak działa narzędzie do zarządzania kluczami tajnymi</span><span class="sxs-lookup"><span data-stu-id="dfe04-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="dfe04-248">Narzędzie Secret Manager ukrywa szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="dfe04-249">Możesz użyć narzędzia bez znajomości tych szczegółów implementacji.</span><span class="sxs-lookup"><span data-stu-id="dfe04-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="dfe04-250">Wartości są przechowywane w pliku JSON w folderze profilu użytkownika komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="dfe04-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="dfe04-251">Windows</span><span class="sxs-lookup"><span data-stu-id="dfe04-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="dfe04-252">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="dfe04-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="dfe04-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="dfe04-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="dfe04-254">Ścieżka systemu plików:</span><span class="sxs-lookup"><span data-stu-id="dfe04-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="dfe04-255">W poprzednich ścieżkach plików Zamień na `<user_secrets_id>` `UserSecretsId` wartość określoną w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="dfe04-256">Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi.</span><span class="sxs-lookup"><span data-stu-id="dfe04-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="dfe04-257">Te szczegóły implementacji mogą ulec zmianie.</span><span class="sxs-lookup"><span data-stu-id="dfe04-257">These implementation details may change.</span></span> <span data-ttu-id="dfe04-258">Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="dfe04-259">Włącz magazyn tajny</span><span class="sxs-lookup"><span data-stu-id="dfe04-259">Enable secret storage</span></span>

<span data-ttu-id="dfe04-260">Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="dfe04-261">Aby użyć kluczy tajnych użytkownika, zdefiniuj `UserSecretsId` element w `PropertyGroup` pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="dfe04-262">Wewnętrzny tekst `UserSecretsId` jest dowolny, ale jest unikatowy dla projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="dfe04-263">Deweloperzy zwykle generują identyfikator GUID dla `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="dfe04-264">W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="dfe04-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="dfe04-265">Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="dfe04-266">Ustaw klucz tajny</span><span class="sxs-lookup"><span data-stu-id="dfe04-266">Set a secret</span></span>

<span data-ttu-id="dfe04-267">Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="dfe04-268">Wpis tajny jest skojarzony z wartością projektu `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="dfe04-269">Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="dfe04-270">W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu z `ServiceApiKey` właściwością.</span><span class="sxs-lookup"><span data-stu-id="dfe04-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="dfe04-271">Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów.</span><span class="sxs-lookup"><span data-stu-id="dfe04-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="dfe04-272">Użyj `--project` opcji, aby podać ścieżkę systemu plików, w której istnieje plik projektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="dfe04-273">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="dfe04-274">Spłaszczanie struktury JSON w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfe04-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="dfe04-275">Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera *secrets.js* w pliku w edytorze tekstów.</span><span class="sxs-lookup"><span data-stu-id="dfe04-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="dfe04-276">Zastąp zawartość *secrets.js* przy użyciu par klucz-wartość, które mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="dfe04-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="dfe04-277">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="dfe04-278">Struktura JSON jest spłaszczona po modyfikacji za pośrednictwem `dotnet user-secrets remove` lub `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="dfe04-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="dfe04-279">Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu.</span><span class="sxs-lookup"><span data-stu-id="dfe04-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="dfe04-280">Zmodyfikowany plik przypomina następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="dfe04-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="dfe04-281">Ustawianie wielu wpisów tajnych</span><span class="sxs-lookup"><span data-stu-id="dfe04-281">Set multiple secrets</span></span>

<span data-ttu-id="dfe04-282">Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="dfe04-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="dfe04-283">W poniższym przykładzie *input.js* zawartości pliku są potoku do `set` polecenia.</span><span class="sxs-lookup"><span data-stu-id="dfe04-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="dfe04-284">Windows</span><span class="sxs-lookup"><span data-stu-id="dfe04-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="dfe04-285">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="dfe04-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="dfe04-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="dfe04-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="dfe04-287">Otwórz powłokę poleceń i wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="dfe04-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="dfe04-288">Dostęp do klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="dfe04-288">Access a secret</span></span>

<span data-ttu-id="dfe04-289">[Interfejs API konfiguracji](xref:fundamentals/configuration/index) zapewnia dostęp do kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dfe04-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="dfe04-290">Jeśli projekt jest przeznaczony .NET Framework, zainstaluj [Microsoft.Extensions.Configwersja. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Pakiet NuGet UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="dfe04-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="dfe04-291">W ASP.NET Core 2,0 lub nowszej Źródło konfiguracji użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt jest wywoływany <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="dfe04-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="dfe04-292">`CreateDefaultBuilder` wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> się, gdy <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> jest <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="dfe04-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="dfe04-293">Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie, wywołując <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> w `Startup` konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="dfe04-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="dfe04-294">Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="dfe04-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="dfe04-295">Wpisy tajne użytkownika można pobrać za pośrednictwem interfejsu API konfiguracji platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="dfe04-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="dfe04-296">Mapuj wpisy tajne do POCO</span><span class="sxs-lookup"><span data-stu-id="dfe04-296">Map secrets to a POCO</span></span>

<span data-ttu-id="dfe04-297">Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="dfe04-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-298">Aby zmapować poprzednie wpisy tajne do POCO, użyj funkcji [powiązania grafu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API konfiguracji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="dfe04-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="dfe04-299">Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp do `ServiceApiKey` wartości właściwości:</span><span class="sxs-lookup"><span data-stu-id="dfe04-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="dfe04-300">Wpisy `Movies:ConnectionString` `Movies:ServiceApiKey` tajne i są mapowane na odpowiednie właściwości w programie `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="dfe04-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="dfe04-301">Zastępowanie ciągów hasłami tajnymi</span><span class="sxs-lookup"><span data-stu-id="dfe04-301">String replacement with secrets</span></span>

<span data-ttu-id="dfe04-302">Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne.</span><span class="sxs-lookup"><span data-stu-id="dfe04-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="dfe04-303">Na przykład parametry połączenia z bazą danych przechowywane w programie *appsettings.json* mogą zawierać hasło dla określonego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="dfe04-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="dfe04-304">Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="dfe04-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="dfe04-305">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="dfe04-306">Usuń `Password` parę klucz-wartość z parametrów połączenia w *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="dfe04-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="dfe04-307">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfe04-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="dfe04-308">Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> właściwości obiektu, <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Aby zakończyć parametry połączenia:</span><span class="sxs-lookup"><span data-stu-id="dfe04-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="dfe04-309">Wystaw wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="dfe04-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-310">Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="dfe04-311">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="dfe04-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="dfe04-312">W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w *secrets.jsna*.</span><span class="sxs-lookup"><span data-stu-id="dfe04-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="dfe04-313">Usuń pojedynczy klucz tajny</span><span class="sxs-lookup"><span data-stu-id="dfe04-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-314">Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="dfe04-315">*secrets.jsaplikacji w* pliku został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:</span><span class="sxs-lookup"><span data-stu-id="dfe04-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="dfe04-316">Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="dfe04-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="dfe04-317">Usuń wszystkie wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="dfe04-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="dfe04-318">Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:</span><span class="sxs-lookup"><span data-stu-id="dfe04-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="dfe04-319">Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z *secrets.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="dfe04-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="dfe04-320">Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="dfe04-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="dfe04-321">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="dfe04-321">Additional resources</span></span>

* <span data-ttu-id="dfe04-322">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16328) , aby uzyskać informacje na temat uzyskiwania dostępu do kluczy tajnych użytkownika z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="dfe04-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end