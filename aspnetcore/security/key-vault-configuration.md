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
ms.openlocfilehash: ab3a462a3e09113e96c6bdd0c034bff3e0bebdfa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588298"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="7fe05-103">Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7fe05-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="7fe05-104">Według [Andrew Stanton-pielęgniarki](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7fe05-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fe05-105">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji ze Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="7fe05-106">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="7fe05-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="7fe05-107">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="7fe05-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="7fe05-108">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="7fe05-109">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="7fe05-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7fe05-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="7fe05-111">Pakiety</span><span class="sxs-lookup"><span data-stu-id="7fe05-111">Packages</span></span>

<span data-ttu-id="7fe05-112">Dodaj odwołania do pakietu dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="7fe05-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="7fe05-113">Azure.Extensions.AspNetCore.Configwersja. Klucz</span><span class="sxs-lookup"><span data-stu-id="7fe05-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="7fe05-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="7fe05-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="7fe05-115">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="7fe05-115">Sample app</span></span>

<span data-ttu-id="7fe05-116">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="7fe05-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="7fe05-117">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="7fe05-118">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fe05-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="7fe05-119">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="7fe05-120">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="7fe05-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="7fe05-121">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="7fe05-122">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="7fe05-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="7fe05-123">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="7fe05-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="7fe05-124">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="7fe05-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="7fe05-125">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7fe05-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7fe05-126">Po uruchomieniu przykładowej aplikacji na komputerze lokalnym w środowisku programistycznym klucze tajne są ładowane z magazynu kluczy tajnych użytkownika lokalnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="7fe05-127">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="7fe05-128">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="7fe05-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="7fe05-129">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="7fe05-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="7fe05-130">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="7fe05-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="7fe05-131">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="7fe05-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="7fe05-132">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="7fe05-133">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="7fe05-134">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="7fe05-135">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="7fe05-136">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="7fe05-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="7fe05-137">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="7fe05-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="7fe05-138">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="7fe05-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="7fe05-139">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="7fe05-140">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="7fe05-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="7fe05-141">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="7fe05-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="7fe05-142">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7fe05-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="7fe05-143">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="7fe05-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="7fe05-144">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="7fe05-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="7fe05-145">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="7fe05-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7fe05-146">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="7fe05-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7fe05-147">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="7fe05-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="7fe05-148">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="7fe05-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="7fe05-149">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="7fe05-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="7fe05-150">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="7fe05-151">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="7fe05-152">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7fe05-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="7fe05-153">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7fe05-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="7fe05-154">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="7fe05-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="7fe05-155">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="7fe05-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="7fe05-156">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="7fe05-157">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="7fe05-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="7fe05-158">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="7fe05-159">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="7fe05-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="7fe05-160">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="7fe05-161">Utwórz certyfikat archiwum PKCS # 12 (*PFX*).</span><span class="sxs-lookup"><span data-stu-id="7fe05-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="7fe05-162">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="7fe05-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="7fe05-163">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7fe05-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="7fe05-164">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="7fe05-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="7fe05-165">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="7fe05-166">Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="7fe05-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="7fe05-167">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="7fe05-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="7fe05-168">Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="7fe05-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="7fe05-169">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7fe05-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="7fe05-170">Przejdź do **przystawki certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="7fe05-171">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="7fe05-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="7fe05-172">Akceptowany jest certyfikat *CER*, *PEM* lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="7fe05-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="7fe05-173">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="7fe05-174">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7fe05-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="7fe05-175">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="7fe05-176">Wybierz pozycję **Zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="7fe05-177">Wybierz pozycję **Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="7fe05-178">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="7fe05-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="7fe05-179">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="7fe05-180">Wybierz przycisk **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="7fe05-181">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-181">Select **OK**.</span></span>
1. <span data-ttu-id="7fe05-182">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-182">Select **Save**.</span></span>
1. <span data-ttu-id="7fe05-183">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="7fe05-183">Deploy the app.</span></span>

<span data-ttu-id="7fe05-184">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="7fe05-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="7fe05-185">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="7fe05-186">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="7fe05-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="7fe05-187">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="7fe05-188">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="7fe05-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="7fe05-189">Aplikacja wywołuje **AddAzureKeyVault** z wartościami dostarczonymi przez *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="7fe05-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="7fe05-190">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="7fe05-190">Example values:</span></span>

* <span data-ttu-id="7fe05-191">Nazwa magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="7fe05-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="7fe05-192">Identyfikator aplikacji: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="7fe05-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="7fe05-193">Odcisk palca certyfikatu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="7fe05-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="7fe05-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7fe05-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="7fe05-195">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7fe05-196">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="7fe05-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="7fe05-197">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="7fe05-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="7fe05-198">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="7fe05-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="7fe05-199">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="7fe05-200">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="7fe05-201">Wprowadź nazwę magazynu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="7fe05-202">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="7fe05-203">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="7fe05-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="7fe05-204">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe05-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="7fe05-205">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="7fe05-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="7fe05-206">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="7fe05-207">Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe05-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="7fe05-208">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="7fe05-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="7fe05-209">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7fe05-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="7fe05-210">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="7fe05-210">The sample app:</span></span>

* <span data-ttu-id="7fe05-211">Tworzy wystąpienie `DefaultAzureCredential` klasy, poświadczenia próbuje uzyskać token dostępu ze środowiska dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="7fe05-212">Utworzono nową [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) z `DefaultAzureCredential` wystąpieniem.</span><span class="sxs-lookup"><span data-stu-id="7fe05-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="7fe05-213">`Azure.Security.KeyVault.Secrets.Secrets`Wystąpienie jest używane z domyślną implementacją programu `Azure.Extensions.AspNetCore.Configuration.Secrets` , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="7fe05-214">Przykładowa wartość nazwy magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="7fe05-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="7fe05-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7fe05-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="7fe05-216">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7fe05-217">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7fe05-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="7fe05-218">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="7fe05-219">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="7fe05-220">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="7fe05-221">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="7fe05-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="7fe05-222">Opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="7fe05-222">Configuration options</span></span>

<span data-ttu-id="7fe05-223">AddAzureKeyVault może akceptować AzureKeyVaultConfigurationOptions:</span><span class="sxs-lookup"><span data-stu-id="7fe05-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="7fe05-224">Właściwość</span><span class="sxs-lookup"><span data-stu-id="7fe05-224">Property</span></span>         | <span data-ttu-id="7fe05-225">Opis</span><span class="sxs-lookup"><span data-stu-id="7fe05-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="7fe05-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` wystąpienie używane do kontrolowania ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="7fe05-227">`Timespan` aby poczekać między kolejnymi próbami sondowania magazynu kluczy pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="7fe05-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="7fe05-228">Wartość domyślna to `null` (konfiguracja nie jest ponownie ładowana).</span><span class="sxs-lookup"><span data-stu-id="7fe05-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="7fe05-229">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="7fe05-229">Use a key name prefix</span></span>

<span data-ttu-id="7fe05-230">AddAzureKeyVault zapewnia Przeciążenie, które akceptuje implementację `Azure.Extensions.AspNetCore.Configuration.Secrets` , która umożliwia kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="7fe05-231">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="7fe05-232">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="7fe05-233">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="7fe05-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="7fe05-234">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="7fe05-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="7fe05-235">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="7fe05-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="7fe05-236">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="7fe05-237">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="7fe05-238">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="7fe05-239">AddAzureKeyVault jest wywoływana z niestandardowym `Azure.Extensions.AspNetCore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="7fe05-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="7fe05-240">`Azure.Extensions.AspNetCore.Configuration.Secrets`Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="7fe05-241">`Load` ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="7fe05-242">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="7fe05-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="7fe05-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="7fe05-243">`GetKey`:</span></span>
  * <span data-ttu-id="7fe05-244">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="7fe05-245">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="7fe05-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="7fe05-246">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="7fe05-247">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="7fe05-248">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="7fe05-249">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="7fe05-250">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="7fe05-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="7fe05-251">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="7fe05-252">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="7fe05-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="7fe05-253">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="7fe05-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="7fe05-254">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="7fe05-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="7fe05-255">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="7fe05-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="7fe05-256">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="7fe05-257">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="7fe05-258">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="7fe05-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="7fe05-259">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="7fe05-260">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="7fe05-261">Możesz również wprowadzić własną <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementację do AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="7fe05-262">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="7fe05-263">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="7fe05-263">Bind an array to a class</span></span>

<span data-ttu-id="7fe05-264">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="7fe05-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="7fe05-265">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="7fe05-266">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="7fe05-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="7fe05-267">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="7fe05-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="7fe05-268">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="7fe05-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="7fe05-269">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="7fe05-270">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="7fe05-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="7fe05-271">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia* Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="7fe05-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="7fe05-272">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="7fe05-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="7fe05-273">Klucz</span><span class="sxs-lookup"><span data-stu-id="7fe05-273">Key</span></span> | <span data-ttu-id="7fe05-274">Wartość</span><span class="sxs-lookup"><span data-stu-id="7fe05-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="7fe05-275">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="7fe05-275">Reload secrets</span></span>

<span data-ttu-id="7fe05-276">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="7fe05-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="7fe05-277">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="7fe05-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="7fe05-278">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="7fe05-278">Disabled and expired secrets</span></span>

<span data-ttu-id="7fe05-279">Wyłączone i wygasłe wpisy tajne generują <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="7fe05-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="7fe05-280">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="7fe05-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7fe05-281">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7fe05-281">Troubleshoot</span></span>

<span data-ttu-id="7fe05-282">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="7fe05-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="7fe05-283">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="7fe05-284">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="7fe05-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="7fe05-285">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="7fe05-286">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="7fe05-287">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="7fe05-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="7fe05-288">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="7fe05-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="7fe05-289">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ) lub usługę Azure AD DirectoryId ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="7fe05-290">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="7fe05-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="7fe05-291">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="7fe05-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7fe05-292">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7fe05-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="7fe05-293">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="7fe05-294">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="7fe05-295">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="7fe05-296">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="7fe05-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="7fe05-297">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="7fe05-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="7fe05-298">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="7fe05-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7fe05-299">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="7fe05-300">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="7fe05-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="7fe05-301">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="7fe05-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="7fe05-302">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="7fe05-303">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="7fe05-304">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7fe05-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="7fe05-305">Pakiety</span><span class="sxs-lookup"><span data-stu-id="7fe05-305">Packages</span></span>

<span data-ttu-id="7fe05-306">Dodaj odwołanie do pakietu do [Microsoft.Extensions.Configwersja. Pakiet AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="7fe05-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="7fe05-307">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="7fe05-307">Sample app</span></span>

<span data-ttu-id="7fe05-308">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="7fe05-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="7fe05-309">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="7fe05-310">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7fe05-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="7fe05-311">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="7fe05-312">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="7fe05-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="7fe05-313">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="7fe05-314">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="7fe05-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="7fe05-315">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="7fe05-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="7fe05-316">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="7fe05-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="7fe05-317">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7fe05-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7fe05-318">Po uruchomieniu przykładowej aplikacji na komputerze lokalnym w środowisku programistycznym klucze tajne są ładowane z magazynu kluczy tajnych użytkownika lokalnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="7fe05-319">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="7fe05-320">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="7fe05-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="7fe05-321">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="7fe05-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="7fe05-322">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="7fe05-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="7fe05-323">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="7fe05-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="7fe05-324">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="7fe05-325">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="7fe05-326">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="7fe05-327">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="7fe05-328">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="7fe05-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="7fe05-329">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="7fe05-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="7fe05-330">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="7fe05-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="7fe05-331">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="7fe05-332">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="7fe05-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="7fe05-333">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="7fe05-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="7fe05-334">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7fe05-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="7fe05-335">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="7fe05-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="7fe05-336">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="7fe05-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="7fe05-337">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="7fe05-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7fe05-338">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="7fe05-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7fe05-339">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="7fe05-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="7fe05-340">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="7fe05-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="7fe05-341">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="7fe05-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="7fe05-342">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="7fe05-343">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="7fe05-344">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7fe05-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="7fe05-345">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7fe05-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="7fe05-346">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="7fe05-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="7fe05-347">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="7fe05-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="7fe05-348">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="7fe05-349">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="7fe05-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="7fe05-350">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="7fe05-351">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="7fe05-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="7fe05-352">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="7fe05-353">Utwórz certyfikat archiwum PKCS # 12 (*PFX*).</span><span class="sxs-lookup"><span data-stu-id="7fe05-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="7fe05-354">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="7fe05-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="7fe05-355">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7fe05-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="7fe05-356">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="7fe05-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="7fe05-357">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="7fe05-358">Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="7fe05-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="7fe05-359">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="7fe05-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="7fe05-360">Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="7fe05-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="7fe05-361">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7fe05-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="7fe05-362">Przejdź do **przystawki certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="7fe05-363">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="7fe05-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="7fe05-364">Akceptowany jest certyfikat *CER*, *PEM* lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="7fe05-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="7fe05-365">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="7fe05-366">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7fe05-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="7fe05-367">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="7fe05-368">Wybierz pozycję **Zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="7fe05-369">Wybierz pozycję **Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="7fe05-370">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="7fe05-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="7fe05-371">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="7fe05-372">Wybierz przycisk **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="7fe05-373">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-373">Select **OK**.</span></span>
1. <span data-ttu-id="7fe05-374">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="7fe05-374">Select **Save**.</span></span>
1. <span data-ttu-id="7fe05-375">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="7fe05-375">Deploy the app.</span></span>

<span data-ttu-id="7fe05-376">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="7fe05-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="7fe05-377">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="7fe05-378">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="7fe05-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="7fe05-379">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="7fe05-380">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="7fe05-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="7fe05-381">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez *appsettings.json* plik:</span><span class="sxs-lookup"><span data-stu-id="7fe05-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="7fe05-382">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="7fe05-382">Example values:</span></span>

* <span data-ttu-id="7fe05-383">Nazwa magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="7fe05-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="7fe05-384">Identyfikator aplikacji: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="7fe05-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="7fe05-385">Odcisk palca certyfikatu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="7fe05-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="7fe05-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7fe05-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="7fe05-387">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7fe05-388">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="7fe05-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="7fe05-389">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="7fe05-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="7fe05-390">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="7fe05-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="7fe05-391">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="7fe05-392">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="7fe05-393">Wprowadź nazwę magazynu w *appsettings.json* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="7fe05-394">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="7fe05-395">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="7fe05-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="7fe05-396">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe05-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="7fe05-397">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="7fe05-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="7fe05-398">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="7fe05-399">Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="7fe05-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="7fe05-400">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="7fe05-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="7fe05-401">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7fe05-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="7fe05-402">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="7fe05-402">The sample app:</span></span>

* <span data-ttu-id="7fe05-403">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="7fe05-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="7fe05-404">Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="7fe05-405"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="7fe05-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="7fe05-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="7fe05-407">Przykładowa wartość nazwy magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="7fe05-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="7fe05-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7fe05-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="7fe05-409">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7fe05-410">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7fe05-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="7fe05-411">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="7fe05-412">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="7fe05-413">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7fe05-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="7fe05-414">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="7fe05-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="7fe05-415">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="7fe05-415">Use a key name prefix</span></span>

<span data-ttu-id="7fe05-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="7fe05-417">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="7fe05-418">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="7fe05-419">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="7fe05-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="7fe05-420">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="7fe05-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="7fe05-421">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="7fe05-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="7fe05-422">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="7fe05-423">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="7fe05-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="7fe05-424">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="7fe05-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="7fe05-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="7fe05-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="7fe05-427">`Load` ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="7fe05-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="7fe05-428">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="7fe05-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="7fe05-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="7fe05-429">`GetKey`:</span></span>
  * <span data-ttu-id="7fe05-430">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="7fe05-431">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="7fe05-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="7fe05-432">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="7fe05-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="7fe05-433">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="7fe05-434">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="7fe05-435">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="7fe05-436">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="7fe05-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="7fe05-437">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="7fe05-438">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="7fe05-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="7fe05-439">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="7fe05-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="7fe05-440">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="7fe05-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="7fe05-441">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="7fe05-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="7fe05-442">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="7fe05-443">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="7fe05-444">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="7fe05-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="7fe05-445">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="7fe05-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="7fe05-446">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="7fe05-447">Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="7fe05-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="7fe05-448">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7fe05-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="7fe05-449">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="7fe05-449">Bind an array to a class</span></span>

<span data-ttu-id="7fe05-450">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="7fe05-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="7fe05-451">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="7fe05-452">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="7fe05-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="7fe05-453">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="7fe05-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="7fe05-454">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="7fe05-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="7fe05-455">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="7fe05-456">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="7fe05-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="7fe05-457">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia* Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="7fe05-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="7fe05-458">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="7fe05-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="7fe05-459">Klucz</span><span class="sxs-lookup"><span data-stu-id="7fe05-459">Key</span></span> | <span data-ttu-id="7fe05-460">Wartość</span><span class="sxs-lookup"><span data-stu-id="7fe05-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="7fe05-461">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="7fe05-461">Reload secrets</span></span>

<span data-ttu-id="7fe05-462">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="7fe05-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="7fe05-463">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="7fe05-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="7fe05-464">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="7fe05-464">Disabled and expired secrets</span></span>

<span data-ttu-id="7fe05-465">Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="7fe05-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="7fe05-466">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="7fe05-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7fe05-467">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7fe05-467">Troubleshoot</span></span>

<span data-ttu-id="7fe05-468">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="7fe05-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="7fe05-469">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7fe05-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="7fe05-470">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="7fe05-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="7fe05-471">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7fe05-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="7fe05-472">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7fe05-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="7fe05-473">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="7fe05-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="7fe05-474">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="7fe05-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="7fe05-475">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="7fe05-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="7fe05-476">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="7fe05-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="7fe05-477">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="7fe05-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7fe05-478">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7fe05-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="7fe05-479">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="7fe05-480">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="7fe05-481">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7fe05-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="7fe05-482">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="7fe05-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="7fe05-483">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="7fe05-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="7fe05-484">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="7fe05-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
