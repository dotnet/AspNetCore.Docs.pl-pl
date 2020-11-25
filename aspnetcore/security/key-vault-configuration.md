---
title: Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core
author: rick-anderson
description: Informacje dotyczące konfigurowania aplikacji przy użyciu par nazwa-wartość ładowanych w czasie wykonywania przy użyciu dostawcy konfiguracji Azure Key Vault.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 7f5cd3de38f1e45d9b188c513a0e62ca658b2992
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035908"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="2a539-103">Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a539-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="2a539-104">Według [Andrew Stanton-pielęgniarki](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2a539-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a539-105">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji ze Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="2a539-106">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="2a539-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="2a539-107">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="2a539-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="2a539-108">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="2a539-109">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="2a539-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a539-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="2a539-111">Pakiety</span><span class="sxs-lookup"><span data-stu-id="2a539-111">Packages</span></span>

<span data-ttu-id="2a539-112">Dodaj odwołanie do pakietu do [Azure.Extensions.AspNetCore.Configwersja. Pakiet kluczy tajnych](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) .</span><span class="sxs-lookup"><span data-stu-id="2a539-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="2a539-113">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="2a539-113">Sample app</span></span>

<span data-ttu-id="2a539-114">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="2a539-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="2a539-115">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="2a539-116">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a539-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="2a539-117">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="2a539-118">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="2a539-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="2a539-119">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="2a539-120">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="2a539-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="2a539-121">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="2a539-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="2a539-122">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="2a539-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="2a539-123">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2a539-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2a539-124">Po uruchomieniu przykładowej aplikacji na komputerze lokalnym w środowisku programistycznym klucze tajne są ładowane z magazynu kluczy tajnych użytkownika lokalnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="2a539-125">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="2a539-126">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="2a539-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="2a539-127">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="2a539-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="2a539-128">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="2a539-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="2a539-129">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="2a539-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="2a539-130">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2a539-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="2a539-131">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="2a539-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="2a539-132">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="2a539-133">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="2a539-134">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="2a539-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="2a539-135">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="2a539-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="2a539-136">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="2a539-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="2a539-137">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="2a539-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="2a539-138">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="2a539-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="2a539-139">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="2a539-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="2a539-140">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2a539-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="2a539-141">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="2a539-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="2a539-142">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="2a539-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="2a539-143">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="2a539-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2a539-144">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="2a539-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2a539-145">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="2a539-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="2a539-146">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="2a539-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="2a539-147">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="2a539-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2a539-148">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="2a539-149">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="2a539-150">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="2a539-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="2a539-151">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a539-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="2a539-152">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="2a539-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="2a539-153">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="2a539-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="2a539-154">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**.</span><span class="sxs-lookup"><span data-stu-id="2a539-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="2a539-155">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="2a539-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="2a539-156">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="2a539-157">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="2a539-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="2a539-158">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="2a539-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="2a539-159">Utwórz certyfikat archiwum PKCS # 12 (*PFX*).</span><span class="sxs-lookup"><span data-stu-id="2a539-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="2a539-160">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="2a539-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="2a539-161">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a539-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="2a539-162">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="2a539-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="2a539-163">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="2a539-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="2a539-164">Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="2a539-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="2a539-165">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="2a539-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="2a539-166">Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="2a539-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="2a539-167">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2a539-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="2a539-168">Przejdź do **przystawki certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="2a539-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="2a539-169">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="2a539-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="2a539-170">Akceptowany jest certyfikat *CER*, *PEM* lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="2a539-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="2a539-171">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="2a539-172">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2a539-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="2a539-173">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="2a539-174">Wybierz pozycję **zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="2a539-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="2a539-175">Wybierz pozycję **Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="2a539-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="2a539-176">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="2a539-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="2a539-177">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="2a539-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="2a539-178">Wybierz przycisk **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="2a539-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="2a539-179">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="2a539-179">Select **OK**.</span></span>
1. <span data-ttu-id="2a539-180">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="2a539-180">Select **Save**.</span></span>
1. <span data-ttu-id="2a539-181">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="2a539-181">Deploy the app.</span></span>

<span data-ttu-id="2a539-182">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="2a539-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="2a539-183">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="2a539-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="2a539-184">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2a539-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="2a539-185">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a539-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="2a539-186">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="2a539-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="2a539-187">Aplikacja wywołuje **AddAzureKeyVault** z wartościami dostarczonymi przez *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="2a539-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="2a539-188">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="2a539-188">Example values:</span></span>

* <span data-ttu-id="2a539-189">Nazwa magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2a539-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="2a539-190">Identyfikator aplikacji: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="2a539-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="2a539-191">Odcisk palca certyfikatu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="2a539-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="2a539-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2a539-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="2a539-193">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2a539-194">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="2a539-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="2a539-195">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="2a539-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="2a539-196">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="2a539-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="2a539-197">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="2a539-198">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="2a539-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="2a539-199">Wprowadź nazwę magazynu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="2a539-200">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2a539-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="2a539-201">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="2a539-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="2a539-202">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2a539-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="2a539-203">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="2a539-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="2a539-204">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="2a539-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="2a539-205">Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="2a539-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="2a539-206">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="2a539-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="2a539-207">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2a539-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="2a539-208">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="2a539-208">The sample app:</span></span>

* <span data-ttu-id="2a539-209">Tworzy wystąpienie `DefaultAzureCredential` klasy, poświadczenia próbuje uzyskać token dostępu ze środowiska dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="2a539-210">Utworzono nową [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) z `DefaultAzureCredential` wystąpieniem.</span><span class="sxs-lookup"><span data-stu-id="2a539-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="2a539-211">`Azure.Security.KeyVault.Secrets.Secrets`Wystąpienie jest używane z domyślną implementacją programu `Azure.Extensions.Aspnetcore.Configuration.Secrets` , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="2a539-212">Przykładowa wartość nazwy magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2a539-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="2a539-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2a539-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="2a539-214">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2a539-215">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a539-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="2a539-216">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="2a539-217">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="2a539-218">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="2a539-219">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="2a539-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="2a539-220">Opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2a539-220">Configuration options</span></span>

<span data-ttu-id="2a539-221">AddAzureKeyVault może akceptować AzureKeyVaultConfigurationOptions:</span><span class="sxs-lookup"><span data-stu-id="2a539-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="2a539-222">Właściwość</span><span class="sxs-lookup"><span data-stu-id="2a539-222">Property</span></span>         | <span data-ttu-id="2a539-223">Opis</span><span class="sxs-lookup"><span data-stu-id="2a539-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="2a539-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` wystąpienie używane do kontrolowania ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="2a539-225">`Timespan` aby poczekać między kolejnymi próbami sondowania magazynu kluczy pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="2a539-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="2a539-226">Wartość domyślna to `null` (konfiguracja nie jest ponownie ładowana).</span><span class="sxs-lookup"><span data-stu-id="2a539-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="2a539-227">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="2a539-227">Use a key name prefix</span></span>

<span data-ttu-id="2a539-228">AddAzureKeyVault zapewnia Przeciążenie, które akceptuje implementację `Azure.Extensions.Aspnetcore.Configuration.Secrets` , która umożliwia kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2a539-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="2a539-229">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="2a539-230">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2a539-231">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="2a539-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="2a539-232">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="2a539-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="2a539-233">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="2a539-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="2a539-234">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="2a539-235">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="2a539-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="2a539-236">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="2a539-237">AddAzureKeyVault jest wywoływana z niestandardowym `Azure.Extensions.Aspnetcore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="2a539-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="2a539-238">`Azure.Extensions.Aspnetcore.Configuration.Secrets`Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a539-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="2a539-239">`Load` ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="2a539-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="2a539-240">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="2a539-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="2a539-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="2a539-241">`GetKey`:</span></span>
  * <span data-ttu-id="2a539-242">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="2a539-243">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="2a539-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="2a539-244">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="2a539-245">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="2a539-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="2a539-246">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="2a539-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="2a539-247">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="2a539-248">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="2a539-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="2a539-249">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="2a539-250">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="2a539-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2a539-251">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="2a539-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="2a539-252">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="2a539-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="2a539-253">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="2a539-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="2a539-254">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="2a539-255">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="2a539-256">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="2a539-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="2a539-257">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="2a539-258">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="2a539-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="2a539-259">Możesz również wprowadzić własną <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementację do AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="2a539-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="2a539-260">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2a539-261">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="2a539-261">Bind an array to a class</span></span>

<span data-ttu-id="2a539-262">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="2a539-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="2a539-263">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="2a539-264">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2a539-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="2a539-265">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="2a539-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="2a539-266">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="2a539-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="2a539-267">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="2a539-268">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="2a539-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="2a539-269">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia* Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="2a539-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="2a539-270">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="2a539-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="2a539-271">Klucz</span><span class="sxs-lookup"><span data-stu-id="2a539-271">Key</span></span> | <span data-ttu-id="2a539-272">Wartość</span><span class="sxs-lookup"><span data-stu-id="2a539-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="2a539-273">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="2a539-273">Reload secrets</span></span>

<span data-ttu-id="2a539-274">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="2a539-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="2a539-275">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="2a539-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="2a539-276">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="2a539-276">Disabled and expired secrets</span></span>

<span data-ttu-id="2a539-277">Wyłączone i wygasłe wpisy tajne generują <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="2a539-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="2a539-278">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="2a539-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2a539-279">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2a539-279">Troubleshoot</span></span>

<span data-ttu-id="2a539-280">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2a539-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2a539-281">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a539-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="2a539-282">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2a539-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="2a539-283">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="2a539-284">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="2a539-285">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="2a539-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="2a539-286">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="2a539-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="2a539-287">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ) lub usługę Azure AD DirectoryId ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="2a539-288">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="2a539-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="2a539-289">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="2a539-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a539-290">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2a539-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="2a539-291">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="2a539-292">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="2a539-293">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="2a539-294">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="2a539-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="2a539-295">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="2a539-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="2a539-296">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="2a539-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a539-297">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="2a539-298">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="2a539-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="2a539-299">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="2a539-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="2a539-300">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="2a539-301">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="2a539-302">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a539-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="2a539-303">Pakiety</span><span class="sxs-lookup"><span data-stu-id="2a539-303">Packages</span></span>

<span data-ttu-id="2a539-304">Dodaj odwołanie do pakietu do [Microsoft.Extensions.Configwersja. Pakiet AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="2a539-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="2a539-305">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="2a539-305">Sample app</span></span>

<span data-ttu-id="2a539-306">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="2a539-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="2a539-307">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="2a539-308">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a539-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="2a539-309">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="2a539-310">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="2a539-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="2a539-311">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="2a539-312">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="2a539-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="2a539-313">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="2a539-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="2a539-314">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="2a539-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="2a539-315">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2a539-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2a539-316">Po uruchomieniu przykładowej aplikacji na komputerze lokalnym w środowisku programistycznym klucze tajne są ładowane z magazynu kluczy tajnych użytkownika lokalnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="2a539-317">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="2a539-318">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="2a539-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="2a539-319">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="2a539-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="2a539-320">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="2a539-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="2a539-321">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="2a539-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="2a539-322">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2a539-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="2a539-323">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="2a539-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="2a539-324">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="2a539-325">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="2a539-326">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="2a539-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="2a539-327">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="2a539-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="2a539-328">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="2a539-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="2a539-329">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="2a539-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="2a539-330">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="2a539-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="2a539-331">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="2a539-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="2a539-332">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2a539-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="2a539-333">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="2a539-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="2a539-334">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="2a539-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="2a539-335">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="2a539-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2a539-336">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="2a539-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2a539-337">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="2a539-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="2a539-338">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="2a539-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="2a539-339">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="2a539-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2a539-340">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="2a539-341">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="2a539-342">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="2a539-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="2a539-343">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a539-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="2a539-344">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="2a539-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="2a539-345">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="2a539-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="2a539-346">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**.</span><span class="sxs-lookup"><span data-stu-id="2a539-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="2a539-347">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="2a539-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="2a539-348">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="2a539-349">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="2a539-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="2a539-350">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="2a539-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="2a539-351">Utwórz certyfikat archiwum PKCS # 12 (*PFX*).</span><span class="sxs-lookup"><span data-stu-id="2a539-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="2a539-352">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="2a539-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="2a539-353">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a539-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="2a539-354">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="2a539-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="2a539-355">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="2a539-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="2a539-356">Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="2a539-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="2a539-357">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="2a539-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="2a539-358">Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="2a539-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="2a539-359">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2a539-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="2a539-360">Przejdź do **przystawki certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="2a539-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="2a539-361">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="2a539-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="2a539-362">Akceptowany jest certyfikat *CER*, *PEM* lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="2a539-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="2a539-363">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="2a539-364">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2a539-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="2a539-365">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="2a539-366">Wybierz pozycję **zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="2a539-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="2a539-367">Wybierz pozycję **Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="2a539-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="2a539-368">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="2a539-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="2a539-369">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="2a539-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="2a539-370">Wybierz przycisk **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="2a539-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="2a539-371">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="2a539-371">Select **OK**.</span></span>
1. <span data-ttu-id="2a539-372">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="2a539-372">Select **Save**.</span></span>
1. <span data-ttu-id="2a539-373">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="2a539-373">Deploy the app.</span></span>

<span data-ttu-id="2a539-374">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="2a539-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="2a539-375">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="2a539-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="2a539-376">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="2a539-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="2a539-377">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a539-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="2a539-378">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="2a539-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="2a539-379">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="2a539-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="2a539-380">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="2a539-380">Example values:</span></span>

* <span data-ttu-id="2a539-381">Nazwa magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2a539-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="2a539-382">Identyfikator aplikacji: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="2a539-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="2a539-383">Odcisk palca certyfikatu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="2a539-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="2a539-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2a539-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="2a539-385">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2a539-386">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="2a539-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="2a539-387">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="2a539-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="2a539-388">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="2a539-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="2a539-389">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="2a539-390">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="2a539-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="2a539-391">Wprowadź nazwę magazynu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="2a539-392">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2a539-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="2a539-393">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="2a539-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="2a539-394">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2a539-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="2a539-395">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="2a539-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="2a539-396">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="2a539-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="2a539-397">Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="2a539-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="2a539-398">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="2a539-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="2a539-399">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2a539-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="2a539-400">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="2a539-400">The sample app:</span></span>

* <span data-ttu-id="2a539-401">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="2a539-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="2a539-402">Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="2a539-403"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="2a539-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="2a539-404"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="2a539-405">Przykładowa wartość nazwy magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2a539-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="2a539-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2a539-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="2a539-407">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2a539-408">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2a539-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="2a539-409">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="2a539-410">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="2a539-411">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="2a539-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="2a539-412">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="2a539-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="2a539-413">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="2a539-413">Use a key name prefix</span></span>

<span data-ttu-id="2a539-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2a539-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="2a539-415">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="2a539-416">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2a539-417">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="2a539-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="2a539-418">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="2a539-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="2a539-419">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="2a539-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="2a539-420">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="2a539-421">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="2a539-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="2a539-422">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="2a539-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="2a539-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="2a539-424"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a539-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="2a539-425">`Load` ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="2a539-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="2a539-426">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="2a539-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="2a539-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="2a539-427">`GetKey`:</span></span>
  * <span data-ttu-id="2a539-428">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="2a539-429">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="2a539-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="2a539-430">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="2a539-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="2a539-431">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="2a539-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="2a539-432">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="2a539-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="2a539-433">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="2a539-434">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="2a539-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="2a539-435">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="2a539-436">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="2a539-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2a539-437">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="2a539-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="2a539-438">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="2a539-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="2a539-439">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="2a539-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="2a539-440">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="2a539-441">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="2a539-442">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="2a539-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="2a539-443">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="2a539-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="2a539-444">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="2a539-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="2a539-445">Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="2a539-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="2a539-446">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2a539-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2a539-447">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="2a539-447">Bind an array to a class</span></span>

<span data-ttu-id="2a539-448">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="2a539-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="2a539-449">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="2a539-450">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2a539-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="2a539-451">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="2a539-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="2a539-452">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="2a539-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="2a539-453">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="2a539-454">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="2a539-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="2a539-455">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia* Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="2a539-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="2a539-456">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="2a539-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="2a539-457">Klucz</span><span class="sxs-lookup"><span data-stu-id="2a539-457">Key</span></span> | <span data-ttu-id="2a539-458">Wartość</span><span class="sxs-lookup"><span data-stu-id="2a539-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="2a539-459">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="2a539-459">Reload secrets</span></span>

<span data-ttu-id="2a539-460">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="2a539-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="2a539-461">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="2a539-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="2a539-462">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="2a539-462">Disabled and expired secrets</span></span>

<span data-ttu-id="2a539-463">Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="2a539-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="2a539-464">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="2a539-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2a539-465">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="2a539-465">Troubleshoot</span></span>

<span data-ttu-id="2a539-466">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2a539-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2a539-467">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="2a539-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="2a539-468">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2a539-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="2a539-469">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2a539-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="2a539-470">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="2a539-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="2a539-471">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="2a539-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="2a539-472">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="2a539-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="2a539-473">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="2a539-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="2a539-474">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="2a539-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="2a539-475">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="2a539-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a539-476">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2a539-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="2a539-477">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="2a539-478">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="2a539-479">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2a539-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="2a539-480">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="2a539-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="2a539-481">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="2a539-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="2a539-482">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="2a539-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
