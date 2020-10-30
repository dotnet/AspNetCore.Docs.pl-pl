---
title: Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core
author: rick-anderson
description: Informacje dotyczące konfigurowania aplikacji przy użyciu par nazwa-wartość ładowanych w czasie wykonywania przy użyciu dostawcy konfiguracji Azure Key Vault.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 10a949831c180f51bc6bb9b8294150a558f9343c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060134"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="b1065-103">Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1065-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="b1065-104">Według [Andrew Stanton-pielęgniarki](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b1065-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1065-105">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="b1065-106">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="b1065-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="b1065-107">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="b1065-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="b1065-108">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="b1065-109">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="b1065-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1065-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="b1065-111">Pakiety</span><span class="sxs-lookup"><span data-stu-id="b1065-111">Packages</span></span>

<span data-ttu-id="b1065-112">Dodaj odwołanie do pakietu do [Microsoft.Extensions.Configwersja. Pakiet AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="b1065-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b1065-113">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="b1065-113">Sample app</span></span>

<span data-ttu-id="b1065-114">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1065-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="b1065-115">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="b1065-116">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1065-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="b1065-117">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="b1065-118">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="b1065-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="b1065-119">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="b1065-120">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="b1065-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="b1065-121">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="b1065-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="b1065-122">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="b1065-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="b1065-123">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b1065-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b1065-124">Gdy aplikacja Przykładowa zostanie uruchomiona na komputerze lokalnym w środowisku deweloperskim, wpisy tajne są ładowane z lokalnego magazynu lokalnych wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="b1065-125">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="b1065-126">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="b1065-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="b1065-127">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="b1065-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="b1065-128">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="b1065-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="b1065-129">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="b1065-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="b1065-130">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1065-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="b1065-131">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="b1065-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="b1065-132">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="b1065-133">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="b1065-134">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1065-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="b1065-135">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="b1065-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="b1065-136">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="b1065-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="b1065-137">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="b1065-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="b1065-138">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="b1065-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="b1065-139">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="b1065-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="b1065-140">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1065-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="b1065-141">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="b1065-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="b1065-142">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="b1065-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="b1065-143">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="b1065-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b1065-144">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="b1065-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b1065-145">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="b1065-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="b1065-146">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="b1065-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="b1065-147">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="b1065-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b1065-148">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="b1065-149">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="b1065-150">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="b1065-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="b1065-151">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1065-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="b1065-152">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="b1065-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="b1065-153">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="b1065-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="b1065-154">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure** .</span><span class="sxs-lookup"><span data-stu-id="b1065-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="b1065-155">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="b1065-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="b1065-156">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="b1065-157">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b1065-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="b1065-158">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="b1065-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="b1065-159">Utwórz certyfikat archiwum PKCS # 12 ( *PFX* ).</span><span class="sxs-lookup"><span data-stu-id="b1065-159">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="b1065-160">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="b1065-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="b1065-161">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1065-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="b1065-162">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="b1065-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="b1065-163">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="b1065-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="b1065-164">Wyeksportuj certyfikat archiwum PKCS # 12 ( *PFX* ) jako certyfikat szyfrowany algorytmem DER ( *CER* ).</span><span class="sxs-lookup"><span data-stu-id="b1065-164">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="b1065-165">Zarejestruj aplikację w usłudze Azure AD ( **rejestracje aplikacji** ).</span><span class="sxs-lookup"><span data-stu-id="b1065-165">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="b1065-166">Przekaż certyfikat szyfrowany algorytmem DER ( *CER* ) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="b1065-166">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="b1065-167">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="b1065-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="b1065-168">Przejdź do **przystawki certyfikaty & wpisy tajne** .</span><span class="sxs-lookup"><span data-stu-id="b1065-168">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="b1065-169">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="b1065-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="b1065-170">Akceptowany jest certyfikat *CER* , *PEM* lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="b1065-170">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="b1065-171">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *:::no-loc(appsettings.json):::* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *:::no-loc(appsettings.json):::* file.</span></span>
1. <span data-ttu-id="b1065-172">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1065-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="b1065-173">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="b1065-174">Wybierz pozycję **zasady dostępu** .</span><span class="sxs-lookup"><span data-stu-id="b1065-174">Select **Access policies** .</span></span>
1. <span data-ttu-id="b1065-175">Wybierz pozycję **Dodaj zasady dostępu** .</span><span class="sxs-lookup"><span data-stu-id="b1065-175">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="b1065-176">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="b1065-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="b1065-177">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="b1065-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="b1065-178">Wybierz przycisk **Wybierz** .</span><span class="sxs-lookup"><span data-stu-id="b1065-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="b1065-179">Wybierz przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="b1065-179">Select **OK** .</span></span>
1. <span data-ttu-id="b1065-180">Wybierz pozycję **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="b1065-180">Select **Save** .</span></span>
1. <span data-ttu-id="b1065-181">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1065-181">Deploy the app.</span></span>

<span data-ttu-id="b1065-182">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="b1065-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="b1065-183">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="b1065-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="b1065-184">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b1065-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="b1065-185">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b1065-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="b1065-186">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="b1065-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="b1065-187">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez *:::no-loc(appsettings.json):::* plik:</span><span class="sxs-lookup"><span data-stu-id="b1065-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="b1065-188">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="b1065-188">Example values:</span></span>

* <span data-ttu-id="b1065-189">Nazwa magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="b1065-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="b1065-190">Identyfikator aplikacji: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="b1065-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="b1065-191">Odcisk palca certyfikatu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="b1065-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="b1065-192">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="b1065-192">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/:::no-loc(appsettings.json):::?highlight=10-12)]

<span data-ttu-id="b1065-193">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b1065-194">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="b1065-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="b1065-195">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="b1065-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="b1065-196">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b1065-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="b1065-197">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="b1065-198">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="b1065-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="b1065-199">Wprowadź nazwę magazynu w *:::no-loc(appsettings.json):::* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-199">Enter the vault name into the app's *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="b1065-200">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b1065-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="b1065-201">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="b1065-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="b1065-202">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b1065-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="b1065-203">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="b1065-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="b1065-204">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="b1065-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="b1065-205">Identyfikator obiektu jest pokazywany w Azure Portal na **:::no-loc(Identity):::** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="b1065-205">The Object ID is shown in the Azure portal on the **:::no-loc(Identity):::** panel of the App Service.</span></span>

<span data-ttu-id="b1065-206">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="b1065-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="b1065-207">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1065-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="b1065-208">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="b1065-208">The sample app:</span></span>

* <span data-ttu-id="b1065-209">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="b1065-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="b1065-210">Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="b1065-211"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="b1065-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="b1065-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="b1065-213">Przykładowa wartość nazwy magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="b1065-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="b1065-214">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="b1065-214">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="b1065-215">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b1065-216">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1065-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="b1065-217">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="b1065-218">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="b1065-219">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="b1065-220">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="b1065-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="b1065-221">Opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="b1065-221">Configuration options</span></span>

<span data-ttu-id="b1065-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> może akceptować <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="b1065-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="b1065-223">Właściwość</span><span class="sxs-lookup"><span data-stu-id="b1065-223">Property</span></span>         | <span data-ttu-id="b1065-224">Opis</span><span class="sxs-lookup"><span data-stu-id="b1065-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="b1065-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> służy do pobierania wartości.</span><span class="sxs-lookup"><span data-stu-id="b1065-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="b1065-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> wystąpienie używane do kontrolowania ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="b1065-227">`Timespan` aby poczekać między kolejnymi próbami sondowania magazynu kluczy pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="b1065-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="b1065-228">Wartość domyślna to `null` (konfiguracja nie jest ponownie ładowana).</span><span class="sxs-lookup"><span data-stu-id="b1065-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="b1065-229">Identyfikator URI magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="b1065-230">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="b1065-230">Use a key name prefix</span></span>

<span data-ttu-id="b1065-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b1065-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="b1065-232">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="b1065-233">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b1065-234">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="b1065-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="b1065-235">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="b1065-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="b1065-236">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="b1065-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="b1065-237">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="b1065-238">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="b1065-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="b1065-239">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="b1065-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="b1065-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="b1065-241"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b1065-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="b1065-242">`Load` ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b1065-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="b1065-243">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="b1065-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="b1065-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="b1065-244">`GetKey`:</span></span>
  * <span data-ttu-id="b1065-245">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="b1065-246">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="b1065-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="b1065-247">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="b1065-248">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="b1065-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="b1065-249">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="b1065-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="b1065-250">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="b1065-251">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="b1065-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="b1065-252">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="b1065-253">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="b1065-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b1065-254">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="b1065-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="b1065-255">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b1065-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="b1065-256">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="b1065-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="b1065-257">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="b1065-258">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="b1065-259">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="b1065-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="b1065-260">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="b1065-261">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="b1065-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="b1065-262">Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="b1065-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="b1065-263">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b1065-264">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="b1065-264">Bind an array to a class</span></span>

<span data-ttu-id="b1065-265">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="b1065-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="b1065-266">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="b1065-267">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b1065-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="b1065-268">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="b1065-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="b1065-269">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="b1065-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="b1065-270">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="b1065-271">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="b1065-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="b1065-272">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia* Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="b1065-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

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

<span data-ttu-id="b1065-273">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="b1065-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="b1065-274">Klucz</span><span class="sxs-lookup"><span data-stu-id="b1065-274">Key</span></span> | <span data-ttu-id="b1065-275">Wartość</span><span class="sxs-lookup"><span data-stu-id="b1065-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="b1065-276">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="b1065-276">Reload secrets</span></span>

<span data-ttu-id="b1065-277">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="b1065-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="b1065-278">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="b1065-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="b1065-279">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="b1065-279">Disabled and expired secrets</span></span>

<span data-ttu-id="b1065-280">Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="b1065-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="b1065-281">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="b1065-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b1065-282">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="b1065-282">Troubleshoot</span></span>

<span data-ttu-id="b1065-283">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b1065-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b1065-284">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b1065-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="b1065-285">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b1065-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="b1065-286">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="b1065-287">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="b1065-288">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="b1065-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="b1065-289">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="b1065-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="b1065-290">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="b1065-291">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="b1065-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="b1065-292">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="b1065-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1065-293">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1065-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b1065-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="b1065-295">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="b1065-296">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="b1065-297">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="b1065-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="b1065-298">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="b1065-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="b1065-299">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="b1065-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1065-300">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="b1065-301">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="b1065-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="b1065-302">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="b1065-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="b1065-303">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="b1065-304">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="b1065-305">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1065-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="b1065-306">Pakiety</span><span class="sxs-lookup"><span data-stu-id="b1065-306">Packages</span></span>

<span data-ttu-id="b1065-307">Dodaj odwołanie do pakietu do [Microsoft.Extensions.Configwersja. Pakiet AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="b1065-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b1065-308">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="b1065-308">Sample app</span></span>

<span data-ttu-id="b1065-309">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b1065-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="b1065-310">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="b1065-311">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1065-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="b1065-312">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="b1065-313">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="b1065-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="b1065-314">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="b1065-315">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="b1065-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="b1065-316">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="b1065-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="b1065-317">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="b1065-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="b1065-318">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b1065-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b1065-319">Gdy aplikacja Przykładowa zostanie uruchomiona na komputerze lokalnym w środowisku deweloperskim, wpisy tajne są ładowane z lokalnego magazynu lokalnych wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="b1065-320">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="b1065-321">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="b1065-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="b1065-322">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="b1065-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="b1065-323">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="b1065-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="b1065-324">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="b1065-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="b1065-325">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1065-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="b1065-326">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="b1065-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="b1065-327">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="b1065-328">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="b1065-329">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1065-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="b1065-330">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="b1065-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="b1065-331">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="b1065-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="b1065-332">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="b1065-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="b1065-333">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="b1065-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="b1065-334">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="b1065-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="b1065-335">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1065-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="b1065-336">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="b1065-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="b1065-337">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="b1065-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="b1065-338">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="b1065-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b1065-339">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="b1065-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b1065-340">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="b1065-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="b1065-341">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="b1065-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="b1065-342">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="b1065-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b1065-343">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="b1065-344">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="b1065-345">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="b1065-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="b1065-346">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1065-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="b1065-347">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="b1065-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="b1065-348">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="b1065-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="b1065-349">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure** .</span><span class="sxs-lookup"><span data-stu-id="b1065-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="b1065-350">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="b1065-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="b1065-351">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="b1065-352">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b1065-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="b1065-353">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="b1065-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="b1065-354">Utwórz certyfikat archiwum PKCS # 12 ( *PFX* ).</span><span class="sxs-lookup"><span data-stu-id="b1065-354">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="b1065-355">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="b1065-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="b1065-356">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1065-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="b1065-357">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="b1065-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="b1065-358">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="b1065-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="b1065-359">Wyeksportuj certyfikat archiwum PKCS # 12 ( *PFX* ) jako certyfikat szyfrowany algorytmem DER ( *CER* ).</span><span class="sxs-lookup"><span data-stu-id="b1065-359">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="b1065-360">Zarejestruj aplikację w usłudze Azure AD ( **rejestracje aplikacji** ).</span><span class="sxs-lookup"><span data-stu-id="b1065-360">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="b1065-361">Przekaż certyfikat szyfrowany algorytmem DER ( *CER* ) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="b1065-361">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="b1065-362">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="b1065-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="b1065-363">Przejdź do **przystawki certyfikaty & wpisy tajne** .</span><span class="sxs-lookup"><span data-stu-id="b1065-363">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="b1065-364">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="b1065-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="b1065-365">Akceptowany jest certyfikat *CER* , *PEM* lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="b1065-365">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="b1065-366">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *:::no-loc(appsettings.json):::* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *:::no-loc(appsettings.json):::* file.</span></span>
1. <span data-ttu-id="b1065-367">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1065-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="b1065-368">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="b1065-369">Wybierz pozycję **zasady dostępu** .</span><span class="sxs-lookup"><span data-stu-id="b1065-369">Select **Access policies** .</span></span>
1. <span data-ttu-id="b1065-370">Wybierz pozycję **Dodaj zasady dostępu** .</span><span class="sxs-lookup"><span data-stu-id="b1065-370">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="b1065-371">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="b1065-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="b1065-372">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="b1065-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="b1065-373">Wybierz przycisk **Wybierz** .</span><span class="sxs-lookup"><span data-stu-id="b1065-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="b1065-374">Wybierz przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="b1065-374">Select **OK** .</span></span>
1. <span data-ttu-id="b1065-375">Wybierz pozycję **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="b1065-375">Select **Save** .</span></span>
1. <span data-ttu-id="b1065-376">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1065-376">Deploy the app.</span></span>

<span data-ttu-id="b1065-377">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="b1065-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="b1065-378">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="b1065-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="b1065-379">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="b1065-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="b1065-380">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b1065-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="b1065-381">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="b1065-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="b1065-382">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez *:::no-loc(appsettings.json):::* plik:</span><span class="sxs-lookup"><span data-stu-id="b1065-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="b1065-383">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="b1065-383">Example values:</span></span>

* <span data-ttu-id="b1065-384">Nazwa magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="b1065-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="b1065-385">Identyfikator aplikacji: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="b1065-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="b1065-386">Odcisk palca certyfikatu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="b1065-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="b1065-387">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="b1065-387">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/:::no-loc(appsettings.json):::?highlight=10-12)]

<span data-ttu-id="b1065-388">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b1065-389">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="b1065-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="b1065-390">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="b1065-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="b1065-391">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b1065-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="b1065-392">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="b1065-393">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="b1065-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="b1065-394">Wprowadź nazwę magazynu w *:::no-loc(appsettings.json):::* pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-394">Enter the vault name into the app's *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="b1065-395">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b1065-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="b1065-396">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="b1065-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="b1065-397">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b1065-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="b1065-398">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="b1065-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="b1065-399">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="b1065-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="b1065-400">Identyfikator obiektu jest pokazywany w Azure Portal na **:::no-loc(Identity):::** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="b1065-400">The Object ID is shown in the Azure portal on the **:::no-loc(Identity):::** panel of the App Service.</span></span>

<span data-ttu-id="b1065-401">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="b1065-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="b1065-402">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1065-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="b1065-403">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="b1065-403">The sample app:</span></span>

* <span data-ttu-id="b1065-404">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="b1065-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="b1065-405">Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="b1065-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="b1065-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="b1065-407"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="b1065-408">Przykładowa wartość nazwy magazynu kluczy: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="b1065-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="b1065-409">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="b1065-409">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="b1065-410">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b1065-411">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1065-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="b1065-412">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="b1065-413">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="b1065-414">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="b1065-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="b1065-415">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="b1065-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="b1065-416">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="b1065-416">Use a key name prefix</span></span>

<span data-ttu-id="b1065-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b1065-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="b1065-418">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="b1065-419">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b1065-420">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="b1065-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="b1065-421">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="b1065-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="b1065-422">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="b1065-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="b1065-423">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="b1065-424">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="b1065-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="b1065-425">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="b1065-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="b1065-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="b1065-427"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b1065-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="b1065-428">`Load` ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b1065-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="b1065-429">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="b1065-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="b1065-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="b1065-430">`GetKey`:</span></span>
  * <span data-ttu-id="b1065-431">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="b1065-432">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="b1065-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="b1065-433">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="b1065-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="b1065-434">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="b1065-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="b1065-435">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="b1065-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="b1065-436">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="b1065-437">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="b1065-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="b1065-438">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="b1065-439">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="b1065-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b1065-440">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="b1065-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="b1065-441">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b1065-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="b1065-442">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="b1065-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="b1065-443">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="b1065-444">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="b1065-445">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="b1065-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="b1065-446">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b1065-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="b1065-447">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="b1065-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="b1065-448">Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="b1065-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="b1065-449">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1065-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b1065-450">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="b1065-450">Bind an array to a class</span></span>

<span data-ttu-id="b1065-451">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="b1065-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="b1065-452">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="b1065-453">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b1065-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="b1065-454">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="b1065-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="b1065-455">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="b1065-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="b1065-456">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="b1065-457">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="b1065-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="b1065-458">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia* Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="b1065-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

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

<span data-ttu-id="b1065-459">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="b1065-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="b1065-460">Klucz</span><span class="sxs-lookup"><span data-stu-id="b1065-460">Key</span></span> | <span data-ttu-id="b1065-461">Wartość</span><span class="sxs-lookup"><span data-stu-id="b1065-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="b1065-462">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="b1065-462">Reload secrets</span></span>

<span data-ttu-id="b1065-463">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="b1065-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="b1065-464">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="b1065-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="b1065-465">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="b1065-465">Disabled and expired secrets</span></span>

<span data-ttu-id="b1065-466">Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="b1065-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="b1065-467">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="b1065-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b1065-468">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="b1065-468">Troubleshoot</span></span>

<span data-ttu-id="b1065-469">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b1065-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b1065-470">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="b1065-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="b1065-471">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b1065-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="b1065-472">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1065-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="b1065-473">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="b1065-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="b1065-474">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="b1065-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="b1065-475">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="b1065-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="b1065-476">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="b1065-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="b1065-477">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="b1065-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="b1065-478">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="b1065-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1065-479">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1065-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b1065-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="b1065-481">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="b1065-482">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b1065-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="b1065-483">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="b1065-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="b1065-484">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="b1065-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="b1065-485">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="b1065-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

