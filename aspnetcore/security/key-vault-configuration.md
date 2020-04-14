---
title: Dostawca konfiguracji usługi Azure Key Vault w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację przy użyciu dostawcy konfiguracji usługi Azure Key Vault przy użyciu par nazwa-wartość załadowanych w czasie wykonywania.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228169"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="a9d2b-103">Dostawca konfiguracji usługi Azure Key Vault w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9d2b-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="a9d2b-104">Przez [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9d2b-105">W tym dokumencie wyjaśniono, jak używać dostawcy konfiguracji [usługi Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do ładowania wartości konfiguracji aplikacji z wpisów tajnych usługi Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a9d2b-106">Usługa Azure Key Vault to usługa oparta na chmurze, która pomaga w ochronie kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a9d2b-107">Typowe scenariusze korzystania z usługi Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a9d2b-108">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a9d2b-109">Spełnienie wymagań dotyczących sprawdzonych modułów zabezpieczeń sprzętu (HSM) fips 140-2 poziomu 2 podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a9d2b-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9d2b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a9d2b-111">Pakiety</span><span class="sxs-lookup"><span data-stu-id="a9d2b-111">Packages</span></span>

<span data-ttu-id="a9d2b-112">Dodaj odwołanie do pakietu [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a9d2b-113">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="a9d2b-113">Sample app</span></span>

<span data-ttu-id="a9d2b-114">Przykładowa aplikacja działa w jednym z dwóch `#define` trybów określonych przez instrukcję u góry *pliku Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a9d2b-115">`Certificate`&ndash; Demonstruje użycie identyfikatora klienta usługi Azure Key Vault i certyfikatu X.509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w usłudze Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a9d2b-116">Tę wersję próbki można uruchomić z dowolnej lokalizacji, wdrożony w usłudze Azure App Service lub dowolnego hosta zdolnego do obsługi aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a9d2b-117">`Managed`&ndash; Pokazuje, jak używać [tożsamości zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) do uwierzytelniania aplikacji w usłudze Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a9d2b-118">Podczas korzystania z tożsamości zarządzanych do uwierzytelniania identyfikator aplikacji usługi Azure AD i hasło (klucz tajny klienta) nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a9d2b-119">Wersja `Managed` próbki musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a9d2b-120">Postępuj zgodnie ze wskazówkami w [sekcji Użyj tożsamości zarządzanych dla zasobów platformy Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a9d2b-121">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji`#define`przy <xref:index#preprocessor-directives-in-sample-code>użyciu dyrektyw preprocesora ( ), zobacz .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a9d2b-122">Tajne przechowywanie w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="a9d2b-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="a9d2b-123">Ustaw wpisy tajne lokalnie za pomocą [narzędzia Menedżer tajny](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a9d2b-124">Gdy przykładowa aplikacja działa na komputerze lokalnym w środowisku programistycznym, wpisy tajne są ładowane z lokalnego magazynu Programu Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="a9d2b-125">Narzędzie Secret Manager `<UserSecretsId>` wymaga właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a9d2b-126">Ustaw wartość właściwości`{GUID}`( ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a9d2b-127">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a9d2b-128">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w [nazwach kluczy konfiguracji ASP.NET Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a9d2b-129">Tajny menedżer jest używany z powłoki polecenia otwartej dla `{SECRET NAME}` katalogu głównego `{SECRET VALUE}` [zawartości](xref:fundamentals/index#content-root)projektu , gdzie jest nazwa i jest wartością:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a9d2b-130">Wykonaj następujące polecenia w powłoce poleceń z [katalogu głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a9d2b-131">Gdy te wpisy tajne są przechowywane w usłudze Azure Key Vault `_dev` w [magazynie tajnym w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Usługi Azure Key Vault, sufiks jest zmieniany na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a9d2b-132">Sufiks zawiera wizualną wskazówkę w danych wyjściowych aplikacji wskazującą źródło wartości konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a9d2b-133">Tajne magazynowanie w środowisku produkcyjnym z usługą Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a9d2b-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a9d2b-134">Instrukcje dostarczone przez [Przewodnik Szybki start: Ustaw i pobierz klucz tajny z usługi Azure Key Vault przy użyciu narzędzia Azure CLI](/azure/key-vault/quick-create-cli) tematu są podsumowane w tym miejscu do tworzenia usługi Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a9d2b-135">Zapoznaj się z tematem, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a9d2b-136">Otwórz powłokę usługi Azure Cloud przy użyciu jednej z następujących metod w [witrynie Azure portal:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a9d2b-137">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a9d2b-138">Użyj ciągu wyszukiwania "Azure CLI" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a9d2b-139">Otwórz powłokę chmury w przeglądarce za pomocą przycisku **Uruchom powłokę cloud** shell.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a9d2b-140">Wybierz przycisk **Powłoki chmury** w menu w prawym górnym rogu witryny Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a9d2b-141">Aby uzyskać więcej informacji, zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure/) i [omówienie usługi Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a9d2b-142">Jeśli nie jesteś jeszcze uwierzytelniony, zaloguj `az login` się za pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a9d2b-143">Utwórz grupę zasobów za `{RESOURCE GROUP NAME}` pomocą następującego polecenia, gdzie jest `{LOCATION}` nazwa grupy zasobów dla nowej grupy zasobów i jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="a9d2b-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a9d2b-144">Utwórz magazyn kluczy w grupie zasobów `{KEY VAULT NAME}` za pomocą następującego polecenia, `{LOCATION}` gdzie jest nazwa nowego magazynu kluczy i jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="a9d2b-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a9d2b-145">Tworzenie wpisów tajnych w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a9d2b-146">Nazwy tajne usługi Azure Key Vault są ograniczone do znaków alfanumeryczne i kresek.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a9d2b-147">Wartości hierarchiczne (sekcje `--` konfiguracji) używają (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a9d2b-148">Dwukropki, które są zwykle używane do rozgraniczenia sekcji z podklucza w [ASP.NET konfiguracji rdzenia,](xref:fundamentals/configuration/index)nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a9d2b-149">W związku z tym dwa kreski są używane i zamienione na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a9d2b-150">Następujące wpisy tajne są do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a9d2b-151">Wartości zawierają `_prod` sufiks, aby `_dev` odróżnić je od wartości sufiksu załadowany w środowisku deweloperskim z wpisów tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a9d2b-152">Zamień `{KEY VAULT NAME}` nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a9d2b-153">Używanie identyfikatora aplikacji i certyfikatu X.509 dla aplikacji nieobjętych platformą Azure</span><span class="sxs-lookup"><span data-stu-id="a9d2b-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a9d2b-154">Skonfiguruj usługę Azure AD, usługę Azure Key Vault i aplikację do używania identyfikatora aplikacji usługi Azure Active Directory i certyfikatu X.509 do uwierzytelniania w magazynie kluczy, **gdy aplikacja jest hostowana poza platformą Azure.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="a9d2b-155">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a9d2b-156">Chociaż przy użyciu identyfikatora aplikacji i certyfikatu X.509 jest obsługiwany dla aplikacji hostowanych na platformie Azure, zalecamy używanie [tożsamości zarządzanych dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a9d2b-157">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji lub w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a9d2b-158">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X.509, gdy `#define` instrukcja w górnej części pliku *Program.cs* jest ustawiona na `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a9d2b-159">Utwórz certyfikat archiwum PKCS#12 (*.pfx*).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="a9d2b-160">Opcje tworzenia certyfikatów obejmują [MakeCert w systemie Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a9d2b-161">Zainstaluj certyfikat w magazynie certyfikatów osobistych bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a9d2b-162">Oznaczenie klucza jako możliwego do wyeksportowania jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a9d2b-163">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a9d2b-164">Wyeksportuj certyfikat archiwum PKCS#12 (*.pfx*) jako certyfikat zakodowany w języku der (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="a9d2b-165">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="a9d2b-166">Przekaż certyfikat zakodowany w stanie DER *(.cer)* do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="a9d2b-167">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a9d2b-168">Przejdź do **wpisów certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="a9d2b-169">Wybierz **pozycję Przekaż certyfikat,** aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a9d2b-170">Akceptowany jest certyfikat *.cer*, *.pem*lub *.crt.*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a9d2b-171">W pliku *appsettings.json* aplikacji przechowuj nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a9d2b-172">Przejdź do **magazynów kluczy** w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a9d2b-173">Wybierz magazyn kluczy utworzony w [magazynie tajnym w środowisku produkcyjnym z sekcją Usługi Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a9d2b-174">Wybierz pozycję **Zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="a9d2b-175">Wybierz **pozycję Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="a9d2b-176">Otwórz **uprawnienia tajne** i podaj aplikacji uprawnienia **Pobierz** i **Lista.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a9d2b-177">Wybierz **pozycję Wybierz głównego zobowiązanego** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a9d2b-178">Wybierz przycisk **Wybierz.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="a9d2b-179">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-179">Select **OK**.</span></span>
1. <span data-ttu-id="a9d2b-180">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-180">Select **Save**.</span></span>
1. <span data-ttu-id="a9d2b-181">Wdrażanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-181">Deploy the app.</span></span>

<span data-ttu-id="a9d2b-182">Przykładowa `Certificate` aplikacja uzyskuje swoje `IConfigurationRoot` wartości konfiguracyjne o takiej samej nazwie jak nazwa tajna:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a9d2b-183">Wartości nieobyaralne: `SecretName` Wartość dla `config["SecretName"]`jest uzyskiwana za pomocą .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a9d2b-184">Wartości hierarchiczne (sekcje): Użyj `:` notacji `GetSection` (dwukropek) lub metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a9d2b-185">Użyj jednej z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a9d2b-186">Certyfikat X.509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a9d2b-187">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> z wartościami dostarczonymi przez plik *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a9d2b-188">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-188">Example values:</span></span>

* <span data-ttu-id="a9d2b-189">Nazwa magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a9d2b-190">Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a9d2b-191">Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a9d2b-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a9d2b-193">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a9d2b-194">W środowisku programistycznym tajne `_dev` wartości ładują się z sufiksem.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a9d2b-195">W środowisku produkcyjnym wartości `_prod` ładują się z przyrostkiem.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a9d2b-196">Używanie tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="a9d2b-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a9d2b-197">**Aplikacja wdrożona na platformie Azure** może korzystać z [tożsamości zarządzanych dla zasobów platformy Azure,](/azure/active-directory/managed-identities-azure-resources/overview)co umożliwia aplikacji uwierzytelnianie za pomocą usługi Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń (identyfikator aplikacji i klucz tajny hasła/klienta) przechowywanych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a9d2b-198">Przykładowa aplikacja używa tożsamości zarządzanych dla `#define` zasobów platformy Azure, gdy instrukcja u `Managed`góry pliku *Program.cs* jest ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a9d2b-199">Wprowadź nazwę przechowalni w pliku *appsettings.json* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a9d2b-200">Przykładowa aplikacja nie wymaga identyfikatora aplikacji i hasła (klucz `Managed` tajny klienta) po ustawieniu wersji, dzięki czemu można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a9d2b-201">Aplikacja jest wdrażana na platformie Azure, a platforma Azure uwierzytelnia aplikację, aby uzyskać dostęp do usługi Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a9d2b-202">Wdrażanie przykładowej aplikacji w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a9d2b-203">Aplikacja wdrożona w usłudze Azure App Service jest automatycznie rejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a9d2b-204">Uzyskaj identyfikator obiektu z wdrożenia do użycia w następującym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a9d2b-205">Identyfikator obiektu jest wyświetlany w witrynie Azure portal w panelu **Tożsamość** usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a9d2b-206">Korzystając z interfejsu wiersza polecenia platformy Azure i `list` `get` identyfikatora obiektu aplikacji, podaj aplikacji i uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a9d2b-207">**Uruchom ponownie aplikację przy** użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub witryny Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a9d2b-208">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-208">The sample app:</span></span>

* <span data-ttu-id="a9d2b-209">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez ciągu połączenia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a9d2b-210">Gdy nie podano parametry połączenia, dostawca próbuje uzyskać token dostępu z tożsamości zarządzanych dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a9d2b-211">Nowy <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest tworzony `AzureServiceTokenProvider` z wywołania zwrotnego tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a9d2b-212">Wystąpienie <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest używane z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> domyślną implementacją, która ładuje wszystkie`--`wartości tajne i`:`zastępuje podwójne kreski ( ) dwukropkami ( ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a9d2b-213">Przykładowa wartość nazwy magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a9d2b-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a9d2b-215">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a9d2b-216">W środowisku programistycznym `_dev` tajne wartości mają sufiks, ponieważ są one dostarczane przez wpisy tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a9d2b-217">W środowisku produkcyjnym wartości `_prod` ładują się z sufiksem, ponieważ są one dostarczane przez usługę Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a9d2b-218">Jeśli zostanie `Access denied` wyświetlony błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i zapewnia dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a9d2b-219">Upewnij się, że usługa została ponownie uruchomiona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a9d2b-220">Aby uzyskać informacje na temat korzystania z dostawcy z tożsamością zarządzaną i potokiem usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Usługi Azure Resource Manager z maszyną wirtualną z tożsamością usługi zarządzanej.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="a9d2b-221">Opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="a9d2b-221">Configuration options</span></span>

<span data-ttu-id="a9d2b-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>może <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>zaakceptować:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="a9d2b-223">Właściwość</span><span class="sxs-lookup"><span data-stu-id="a9d2b-223">Property</span></span>         | <span data-ttu-id="a9d2b-224">Opis</span><span class="sxs-lookup"><span data-stu-id="a9d2b-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="a9d2b-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>do pobierania wartości.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="a9d2b-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instancji używanej do kontrolowania ładowania tajnego.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="a9d2b-227">`Timespan`czekać między próbami sondowania magazynu kluczy dla zmian.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="a9d2b-228">Wartością domyślną jest `null` (konfiguracja nie jest ponownie załadowana).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="a9d2b-229">Identyfikator URI magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a9d2b-230">Używanie prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="a9d2b-230">Use a key name prefix</span></span>

<span data-ttu-id="a9d2b-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia przeciążenie, które akceptuje <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementację programu , co pozwala kontrolować sposób konwersji wpisów tajnych magazynu kluczy na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a9d2b-232">Na przykład można zaimplementować interfejs, aby załadować wartości tajne na podstawie wartości prefiksu, które są podane podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a9d2b-233">Dzięki temu można na przykład załadować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a9d2b-234">Nie używaj prefiksów w kluczu do wpisów tajnych magazynu kluczy, aby umieścić wpisy tajne dla wielu aplikacji w tym samym magazynie kluczy lub umieścić wpisy tajne środowiska (na przykład *wpisy* tajne rozwoju i *produkcji)* w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a9d2b-235">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperów/produkcji używać oddzielnych magazynów kluczy do izolowania środowisk aplikacji dla najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a9d2b-236">W poniższym przykładzie klucz tajny jest ustanawiany w magazynie kluczy (i `5000-AppSecret` przy użyciu narzędzia Menedżera tajnego dla środowiska programistycznego) dla (okresy nie są dozwolone w nazwach tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a9d2b-237">Ten klucz tajny reprezentuje klucz tajny aplikacji dla wersji 5.0.0.0 aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a9d2b-238">W przypadku innej wersji aplikacji, 5.1.0.0, klucz tajny jest dodawany do magazynu `5100-AppSecret`kluczy (i za pomocą narzędzia Secret Manager) dla .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a9d2b-239">Każda wersja aplikacji ładuje swoją wersją `AppSecret`tajną wartość do swojej konfiguracji jako , odpędzając wersję, ponieważ ładuje klucz tajny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a9d2b-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>nazywa się z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>niestandardowym:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a9d2b-241">Implementacja <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na prefiksy wersji wpisów tajnych, aby załadować odpowiedni klucz tajny do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a9d2b-242">`Load`ładuje klucz tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a9d2b-243">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a9d2b-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-244">`GetKey`:</span></span>
  * <span data-ttu-id="a9d2b-245">Usuwa prefiks z tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a9d2b-246">Zastępuje dwie kreski w dowolnej `KeyDelimiter`nazwie , który jest ogranicznikiem używanym w konfiguracji (zwykle dwukropek).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a9d2b-247">Usługa Azure Key Vault nie zezwala na dwukropek w tajnych nazwach.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a9d2b-248">Metoda `Load` jest wywoływana przez algorytm dostawcy, który iteruje za pośrednictwem wpisów tajnych magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a9d2b-249">Po znalezieniu prefiksu wersji z `Load` `GetKey` , algorytm używa metody, aby zwrócić nazwę konfiguracji nazwy tajnej.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a9d2b-250">Usuwa prefiks wersji z nazwy klucza tajnego i zwraca pozostałą część nazwy tajnej do ładowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a9d2b-251">Gdy takie podejście jest wdrażane:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="a9d2b-252">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a9d2b-253">W poniższym przykładzie wersja aplikacji jest `5.0.0.0`ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a9d2b-254">Upewnij się, że `<UserSecretsId>` właściwość jest obecna w `{GUID}` pliku projektu aplikacji, gdzie jest identyfikator GUID dostarczony przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a9d2b-255">Zapisz następujące wpisy tajne lokalnie za pomocą [narzędzia Secret Manager:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a9d2b-256">Wpisy tajne są zapisywane w usłudze Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a9d2b-257">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a9d2b-258">Klucz tajny `5000-AppSecret` ciągu jest dopasowywał się do wersji aplikacji określonej w pliku projektu aplikacji (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a9d2b-259">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a9d2b-260">W całej aplikacji odczyt konfiguracji `AppSecret` z kluczem ładuje wartość tajną.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a9d2b-261">Jeśli wersja aplikacji zostanie zmieniona w `5.1.0.0` pliku projektu i aplikacja jest uruchamiana `5.1.0.0_secret_value_dev` ponownie, wartość `5.1.0.0_secret_value_prod` klucza tajnego zwracana jest w środowisku deweloperskim i w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a9d2b-262">Można również podać <xref:Microsoft.Azure.KeyVault.KeyVaultClient> własną <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementację do .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a9d2b-263">Klient niestandardowy zezwala na udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a9d2b-264">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="a9d2b-264">Bind an array to a class</span></span>

<span data-ttu-id="a9d2b-265">Dostawca jest w stanie odczytywać wartości konfiguracji do tablicy do powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a9d2b-266">Podczas odczytu ze źródła konfiguracji, które`:`umożliwia klucze zawierają separatory dwukropka ( ) segment`:0:` `:1:`klucza numerycznego służy do rozróżniania klawiszy tworzących tablicę ( , , &hellip; `:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a9d2b-267">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a9d2b-268">Klucze usługi Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a9d2b-269">Podejście opisane w tym temacie używa podwójnych kresek (`--`) jako separatora dla wartości hierarchicznych (sekcje).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a9d2b-270">Klucze tablic są przechowywane w usłudze Azure Key Vault`--0--` `--1--`z &hellip; `--{n}--`podwójnymi myślnikami i segmentami kluczy numerycznych ( , , ).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a9d2b-271">Sprawdź następującą konfigurację dostawcy rejestrowania [serilogu](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a9d2b-272">Istnieją dwa literały obiektu zdefiniowane w `WriteTo` tablicy, które odzwierciedlają dwa ujścia Serilog , które opisują miejsc docelowych dla rejestrowania danych *wyjściowych:*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="a9d2b-273">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana`--`w usłudze Azure Key Vault przy użyciu notacji podwójnej kreski ( ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a9d2b-274">Klucz</span><span class="sxs-lookup"><span data-stu-id="a9d2b-274">Key</span></span> | <span data-ttu-id="a9d2b-275">Wartość</span><span class="sxs-lookup"><span data-stu-id="a9d2b-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a9d2b-276">Przeładuj sekrety</span><span class="sxs-lookup"><span data-stu-id="a9d2b-276">Reload secrets</span></span>

<span data-ttu-id="a9d2b-277">Wpisy tajne `IConfigurationRoot.Reload()` są buforowane, dopóki nie zostanie wywołana.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a9d2b-278">Wygasłe, wyłączone i zaktualizowane wpisy tajne w magazynie `Reload` kluczy nie są przestrzegane przez aplikację, dopóki nie zostanie wykonana.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a9d2b-279">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="a9d2b-279">Disabled and expired secrets</span></span>

<span data-ttu-id="a9d2b-280">Wyłączone i wygasłe <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>wpisy tajne rzucać .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a9d2b-281">Aby zapobiec zrzucaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj wyłączony lub wygasły klucz tajny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a9d2b-282">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="a9d2b-282">Troubleshoot</span></span>

<span data-ttu-id="a9d2b-283">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywany w [ASP.NET infrastruktury rejestrowania rdzenia](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a9d2b-284">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a9d2b-285">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w usłudze Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a9d2b-286">Magazyn kluczy nie istnieje w usłudze Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a9d2b-287">Aplikacja nie jest autoryzowana do uzyskiwania dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a9d2b-288">Zasady dostępu nie obejmują `Get` `List` i uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a9d2b-289">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są niepoprawnie nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a9d2b-290">Aplikacja ma nieprawidłową nazwę`KeyVaultName`magazynu kluczy (`AzureADApplicationId`), identyfikator aplikacji usługi`AzureADCertThumbprint`Azure AD ( lub odcisk palca certyfikatu usługi Azure AD ( ).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a9d2b-291">Klucz konfiguracji (nazwa) jest niepoprawna w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a9d2b-292">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy została utworzona zasada, ale przycisk **Zapisz** nie został wybrany w interfejsie użytkownika **zasad dostępu.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9d2b-293">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a9d2b-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a9d2b-294">Microsoft Azure: Przechowalnia kluczy</span><span class="sxs-lookup"><span data-stu-id="a9d2b-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a9d2b-295">Microsoft Azure: dokumentacja magazynu kluczy</span><span class="sxs-lookup"><span data-stu-id="a9d2b-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a9d2b-296">Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a9d2b-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a9d2b-297">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="a9d2b-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a9d2b-298">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="a9d2b-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a9d2b-299">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="a9d2b-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9d2b-300">W tym dokumencie wyjaśniono, jak używać dostawcy konfiguracji [usługi Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do ładowania wartości konfiguracji aplikacji z wpisów tajnych usługi Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a9d2b-301">Usługa Azure Key Vault to usługa oparta na chmurze, która pomaga w ochronie kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a9d2b-302">Typowe scenariusze korzystania z usługi Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a9d2b-303">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a9d2b-304">Spełnienie wymagań dotyczących sprawdzonych modułów zabezpieczeń sprzętu (HSM) fips 140-2 poziomu 2 podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a9d2b-305">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9d2b-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a9d2b-306">Pakiety</span><span class="sxs-lookup"><span data-stu-id="a9d2b-306">Packages</span></span>

<span data-ttu-id="a9d2b-307">Dodaj odwołanie do pakietu [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a9d2b-308">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="a9d2b-308">Sample app</span></span>

<span data-ttu-id="a9d2b-309">Przykładowa aplikacja działa w jednym z dwóch `#define` trybów określonych przez instrukcję u góry *pliku Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a9d2b-310">`Certificate`&ndash; Demonstruje użycie identyfikatora klienta usługi Azure Key Vault i certyfikatu X.509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w usłudze Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a9d2b-311">Tę wersję próbki można uruchomić z dowolnej lokalizacji, wdrożony w usłudze Azure App Service lub dowolnego hosta zdolnego do obsługi aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a9d2b-312">`Managed`&ndash; Pokazuje, jak używać [tożsamości zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) do uwierzytelniania aplikacji w usłudze Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a9d2b-313">Podczas korzystania z tożsamości zarządzanych do uwierzytelniania identyfikator aplikacji usługi Azure AD i hasło (klucz tajny klienta) nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a9d2b-314">Wersja `Managed` próbki musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a9d2b-315">Postępuj zgodnie ze wskazówkami w [sekcji Użyj tożsamości zarządzanych dla zasobów platformy Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a9d2b-316">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji`#define`przy <xref:index#preprocessor-directives-in-sample-code>użyciu dyrektyw preprocesora ( ), zobacz .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a9d2b-317">Tajne przechowywanie w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="a9d2b-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="a9d2b-318">Ustaw wpisy tajne lokalnie za pomocą [narzędzia Menedżer tajny](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a9d2b-319">Gdy przykładowa aplikacja działa na komputerze lokalnym w środowisku programistycznym, wpisy tajne są ładowane z lokalnego magazynu Programu Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="a9d2b-320">Narzędzie Secret Manager `<UserSecretsId>` wymaga właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a9d2b-321">Ustaw wartość właściwości`{GUID}`( ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a9d2b-322">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a9d2b-323">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w [nazwach kluczy konfiguracji ASP.NET Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a9d2b-324">Tajny menedżer jest używany z powłoki polecenia otwartej dla `{SECRET NAME}` katalogu głównego `{SECRET VALUE}` [zawartości](xref:fundamentals/index#content-root)projektu , gdzie jest nazwa i jest wartością:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a9d2b-325">Wykonaj następujące polecenia w powłoce poleceń z [katalogu głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a9d2b-326">Gdy te wpisy tajne są przechowywane w usłudze Azure Key Vault `_dev` w [magazynie tajnym w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Usługi Azure Key Vault, sufiks jest zmieniany na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a9d2b-327">Sufiks zawiera wizualną wskazówkę w danych wyjściowych aplikacji wskazującą źródło wartości konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a9d2b-328">Tajne magazynowanie w środowisku produkcyjnym z usługą Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a9d2b-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a9d2b-329">Instrukcje dostarczone przez [Przewodnik Szybki start: Ustaw i pobierz klucz tajny z usługi Azure Key Vault przy użyciu narzędzia Azure CLI](/azure/key-vault/quick-create-cli) tematu są podsumowane w tym miejscu do tworzenia usługi Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a9d2b-330">Zapoznaj się z tematem, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a9d2b-331">Otwórz powłokę usługi Azure Cloud przy użyciu jednej z następujących metod w [witrynie Azure portal:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a9d2b-332">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a9d2b-333">Użyj ciągu wyszukiwania "Azure CLI" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a9d2b-334">Otwórz powłokę chmury w przeglądarce za pomocą przycisku **Uruchom powłokę cloud** shell.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a9d2b-335">Wybierz przycisk **Powłoki chmury** w menu w prawym górnym rogu witryny Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a9d2b-336">Aby uzyskać więcej informacji, zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure/) i [omówienie usługi Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a9d2b-337">Jeśli nie jesteś jeszcze uwierzytelniony, zaloguj `az login` się za pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a9d2b-338">Utwórz grupę zasobów za `{RESOURCE GROUP NAME}` pomocą następującego polecenia, gdzie jest `{LOCATION}` nazwa grupy zasobów dla nowej grupy zasobów i jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="a9d2b-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a9d2b-339">Utwórz magazyn kluczy w grupie zasobów `{KEY VAULT NAME}` za pomocą następującego polecenia, `{LOCATION}` gdzie jest nazwa nowego magazynu kluczy i jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="a9d2b-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a9d2b-340">Tworzenie wpisów tajnych w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a9d2b-341">Nazwy tajne usługi Azure Key Vault są ograniczone do znaków alfanumeryczne i kresek.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a9d2b-342">Wartości hierarchiczne (sekcje `--` konfiguracji) używają (dwóch kresek) jako separatora.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a9d2b-343">Dwukropki, które są zwykle używane do rozgraniczenia sekcji z podklucza w [ASP.NET konfiguracji rdzenia,](xref:fundamentals/configuration/index)nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a9d2b-344">W związku z tym dwa kreski są używane i zamienione na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a9d2b-345">Następujące wpisy tajne są do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a9d2b-346">Wartości zawierają `_prod` sufiks, aby `_dev` odróżnić je od wartości sufiksu załadowany w środowisku deweloperskim z wpisów tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a9d2b-347">Zamień `{KEY VAULT NAME}` nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a9d2b-348">Używanie identyfikatora aplikacji i certyfikatu X.509 dla aplikacji nieobjętych platformą Azure</span><span class="sxs-lookup"><span data-stu-id="a9d2b-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a9d2b-349">Skonfiguruj usługę Azure AD, usługę Azure Key Vault i aplikację do używania identyfikatora aplikacji usługi Azure Active Directory i certyfikatu X.509 do uwierzytelniania w magazynie kluczy, **gdy aplikacja jest hostowana poza platformą Azure.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="a9d2b-350">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a9d2b-351">Chociaż przy użyciu identyfikatora aplikacji i certyfikatu X.509 jest obsługiwany dla aplikacji hostowanych na platformie Azure, zalecamy używanie [tożsamości zarządzanych dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a9d2b-352">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji lub w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a9d2b-353">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X.509, gdy `#define` instrukcja w górnej części pliku *Program.cs* jest ustawiona na `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a9d2b-354">Utwórz certyfikat archiwum PKCS#12 (*.pfx*).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="a9d2b-355">Opcje tworzenia certyfikatów obejmują [MakeCert w systemie Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a9d2b-356">Zainstaluj certyfikat w magazynie certyfikatów osobistych bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a9d2b-357">Oznaczenie klucza jako możliwego do wyeksportowania jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a9d2b-358">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a9d2b-359">Wyeksportuj certyfikat archiwum PKCS#12 (*.pfx*) jako certyfikat zakodowany w języku der (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="a9d2b-360">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="a9d2b-361">Przekaż certyfikat zakodowany w stanie DER *(.cer)* do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="a9d2b-362">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a9d2b-363">Przejdź do **wpisów certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="a9d2b-364">Wybierz **pozycję Przekaż certyfikat,** aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a9d2b-365">Akceptowany jest certyfikat *.cer*, *.pem*lub *.crt.*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a9d2b-366">W pliku *appsettings.json* aplikacji przechowuj nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a9d2b-367">Przejdź do **magazynów kluczy** w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a9d2b-368">Wybierz magazyn kluczy utworzony w [magazynie tajnym w środowisku produkcyjnym z sekcją Usługi Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a9d2b-369">Wybierz pozycję **Zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="a9d2b-370">Wybierz **pozycję Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="a9d2b-371">Otwórz **uprawnienia tajne** i podaj aplikacji uprawnienia **Pobierz** i **Lista.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a9d2b-372">Wybierz **pozycję Wybierz głównego zobowiązanego** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a9d2b-373">Wybierz przycisk **Wybierz.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="a9d2b-374">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-374">Select **OK**.</span></span>
1. <span data-ttu-id="a9d2b-375">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-375">Select **Save**.</span></span>
1. <span data-ttu-id="a9d2b-376">Wdrażanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-376">Deploy the app.</span></span>

<span data-ttu-id="a9d2b-377">Przykładowa `Certificate` aplikacja uzyskuje swoje `IConfigurationRoot` wartości konfiguracyjne o takiej samej nazwie jak nazwa tajna:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a9d2b-378">Wartości nieobyaralne: `SecretName` Wartość dla `config["SecretName"]`jest uzyskiwana za pomocą .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a9d2b-379">Wartości hierarchiczne (sekcje): Użyj `:` notacji `GetSection` (dwukropek) lub metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a9d2b-380">Użyj jednej z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a9d2b-381">Certyfikat X.509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a9d2b-382">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> z wartościami dostarczonymi przez plik *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a9d2b-383">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-383">Example values:</span></span>

* <span data-ttu-id="a9d2b-384">Nazwa magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a9d2b-385">Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a9d2b-386">Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a9d2b-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a9d2b-388">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a9d2b-389">W środowisku programistycznym tajne `_dev` wartości ładują się z sufiksem.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a9d2b-390">W środowisku produkcyjnym wartości `_prod` ładują się z przyrostkiem.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a9d2b-391">Używanie tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="a9d2b-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a9d2b-392">**Aplikacja wdrożona na platformie Azure** może korzystać z [tożsamości zarządzanych dla zasobów platformy Azure,](/azure/active-directory/managed-identities-azure-resources/overview)co umożliwia aplikacji uwierzytelnianie za pomocą usługi Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń (identyfikator aplikacji i klucz tajny hasła/klienta) przechowywanych w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a9d2b-393">Przykładowa aplikacja używa tożsamości zarządzanych dla `#define` zasobów platformy Azure, gdy instrukcja u `Managed`góry pliku *Program.cs* jest ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a9d2b-394">Wprowadź nazwę przechowalni w pliku *appsettings.json* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a9d2b-395">Przykładowa aplikacja nie wymaga identyfikatora aplikacji i hasła (klucz `Managed` tajny klienta) po ustawieniu wersji, dzięki czemu można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a9d2b-396">Aplikacja jest wdrażana na platformie Azure, a platforma Azure uwierzytelnia aplikację, aby uzyskać dostęp do usługi Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a9d2b-397">Wdrażanie przykładowej aplikacji w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a9d2b-398">Aplikacja wdrożona w usłudze Azure App Service jest automatycznie rejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a9d2b-399">Uzyskaj identyfikator obiektu z wdrożenia do użycia w następującym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a9d2b-400">Identyfikator obiektu jest wyświetlany w witrynie Azure portal w panelu **Tożsamość** usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a9d2b-401">Korzystając z interfejsu wiersza polecenia platformy Azure i `list` `get` identyfikatora obiektu aplikacji, podaj aplikacji i uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a9d2b-402">**Uruchom ponownie aplikację przy** użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub witryny Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a9d2b-403">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-403">The sample app:</span></span>

* <span data-ttu-id="a9d2b-404">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez ciągu połączenia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a9d2b-405">Gdy nie podano parametry połączenia, dostawca próbuje uzyskać token dostępu z tożsamości zarządzanych dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a9d2b-406">Nowy <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest tworzony `AzureServiceTokenProvider` z wywołania zwrotnego tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a9d2b-407">Wystąpienie <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest używane z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> domyślną implementacją, która ładuje wszystkie`--`wartości tajne i`:`zastępuje podwójne kreski ( ) dwukropkami ( ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a9d2b-408">Przykładowa wartość nazwy magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="a9d2b-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a9d2b-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a9d2b-410">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a9d2b-411">W środowisku programistycznym `_dev` tajne wartości mają sufiks, ponieważ są one dostarczane przez wpisy tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a9d2b-412">W środowisku produkcyjnym wartości `_prod` ładują się z sufiksem, ponieważ są one dostarczane przez usługę Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a9d2b-413">Jeśli zostanie `Access denied` wyświetlony błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i zapewnia dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a9d2b-414">Upewnij się, że usługa została ponownie uruchomiona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a9d2b-415">Aby uzyskać informacje na temat korzystania z dostawcy z tożsamością zarządzaną i potokiem usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Usługi Azure Resource Manager z maszyną wirtualną z tożsamością usługi zarządzanej.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a9d2b-416">Używanie prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="a9d2b-416">Use a key name prefix</span></span>

<span data-ttu-id="a9d2b-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia przeciążenie, które akceptuje <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementację programu , co pozwala kontrolować sposób konwersji wpisów tajnych magazynu kluczy na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a9d2b-418">Na przykład można zaimplementować interfejs, aby załadować wartości tajne na podstawie wartości prefiksu, które są podane podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a9d2b-419">Dzięki temu można na przykład załadować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a9d2b-420">Nie używaj prefiksów w kluczu do wpisów tajnych magazynu kluczy, aby umieścić wpisy tajne dla wielu aplikacji w tym samym magazynie kluczy lub umieścić wpisy tajne środowiska (na przykład *wpisy* tajne rozwoju i *produkcji)* w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a9d2b-421">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperów/produkcji używać oddzielnych magazynów kluczy do izolowania środowisk aplikacji dla najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a9d2b-422">W poniższym przykładzie klucz tajny jest ustanawiany w magazynie kluczy (i `5000-AppSecret` przy użyciu narzędzia Menedżera tajnego dla środowiska programistycznego) dla (okresy nie są dozwolone w nazwach tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a9d2b-423">Ten klucz tajny reprezentuje klucz tajny aplikacji dla wersji 5.0.0.0 aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a9d2b-424">W przypadku innej wersji aplikacji, 5.1.0.0, klucz tajny jest dodawany do magazynu `5100-AppSecret`kluczy (i za pomocą narzędzia Secret Manager) dla .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a9d2b-425">Każda wersja aplikacji ładuje swoją wersją `AppSecret`tajną wartość do swojej konfiguracji jako , odpędzając wersję, ponieważ ładuje klucz tajny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a9d2b-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>nazywa się z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>niestandardowym:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a9d2b-427">Implementacja <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na prefiksy wersji wpisów tajnych, aby załadować odpowiedni klucz tajny do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a9d2b-428">`Load`ładuje klucz tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a9d2b-429">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a9d2b-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-430">`GetKey`:</span></span>
  * <span data-ttu-id="a9d2b-431">Usuwa prefiks z tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a9d2b-432">Zastępuje dwie kreski w dowolnej `KeyDelimiter`nazwie , który jest ogranicznikiem używanym w konfiguracji (zwykle dwukropek).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a9d2b-433">Usługa Azure Key Vault nie zezwala na dwukropek w tajnych nazwach.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a9d2b-434">Metoda `Load` jest wywoływana przez algorytm dostawcy, który iteruje za pośrednictwem wpisów tajnych magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a9d2b-435">Po znalezieniu prefiksu wersji z `Load` `GetKey` , algorytm używa metody, aby zwrócić nazwę konfiguracji nazwy tajnej.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a9d2b-436">Usuwa prefiks wersji z nazwy klucza tajnego i zwraca pozostałą część nazwy tajnej do ładowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a9d2b-437">Gdy takie podejście jest wdrażane:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="a9d2b-438">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a9d2b-439">W poniższym przykładzie wersja aplikacji jest `5.0.0.0`ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a9d2b-440">Upewnij się, że `<UserSecretsId>` właściwość jest obecna w `{GUID}` pliku projektu aplikacji, gdzie jest identyfikator GUID dostarczony przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a9d2b-441">Zapisz następujące wpisy tajne lokalnie za pomocą [narzędzia Secret Manager:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="a9d2b-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a9d2b-442">Wpisy tajne są zapisywane w usłudze Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a9d2b-443">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a9d2b-444">Klucz tajny `5000-AppSecret` ciągu jest dopasowywał się do wersji aplikacji określonej w pliku projektu aplikacji (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a9d2b-445">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a9d2b-446">W całej aplikacji odczyt konfiguracji `AppSecret` z kluczem ładuje wartość tajną.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a9d2b-447">Jeśli wersja aplikacji zostanie zmieniona w `5.1.0.0` pliku projektu i aplikacja jest uruchamiana `5.1.0.0_secret_value_dev` ponownie, wartość `5.1.0.0_secret_value_prod` klucza tajnego zwracana jest w środowisku deweloperskim i w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a9d2b-448">Można również podać <xref:Microsoft.Azure.KeyVault.KeyVaultClient> własną <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementację do .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a9d2b-449">Klient niestandardowy zezwala na udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a9d2b-450">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="a9d2b-450">Bind an array to a class</span></span>

<span data-ttu-id="a9d2b-451">Dostawca jest w stanie odczytywać wartości konfiguracji do tablicy do powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a9d2b-452">Podczas odczytu ze źródła konfiguracji, które`:`umożliwia klucze zawierają separatory dwukropka ( ) segment`:0:` `:1:`klucza numerycznego służy do rozróżniania klawiszy tworzących tablicę ( , , &hellip; `:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a9d2b-453">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a9d2b-454">Klucze usługi Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a9d2b-455">Podejście opisane w tym temacie używa podwójnych kresek (`--`) jako separatora dla wartości hierarchicznych (sekcje).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a9d2b-456">Klucze tablic są przechowywane w usłudze Azure Key Vault`--0--` `--1--`z &hellip; `--{n}--`podwójnymi myślnikami i segmentami kluczy numerycznych ( , , ).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a9d2b-457">Sprawdź następującą konfigurację dostawcy rejestrowania [serilogu](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a9d2b-458">Istnieją dwa literały obiektu zdefiniowane w `WriteTo` tablicy, które odzwierciedlają dwa ujścia Serilog , które opisują miejsc docelowych dla rejestrowania danych *wyjściowych:*</span><span class="sxs-lookup"><span data-stu-id="a9d2b-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="a9d2b-459">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana`--`w usłudze Azure Key Vault przy użyciu notacji podwójnej kreski ( ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a9d2b-460">Klucz</span><span class="sxs-lookup"><span data-stu-id="a9d2b-460">Key</span></span> | <span data-ttu-id="a9d2b-461">Wartość</span><span class="sxs-lookup"><span data-stu-id="a9d2b-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a9d2b-462">Przeładuj sekrety</span><span class="sxs-lookup"><span data-stu-id="a9d2b-462">Reload secrets</span></span>

<span data-ttu-id="a9d2b-463">Wpisy tajne `IConfigurationRoot.Reload()` są buforowane, dopóki nie zostanie wywołana.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a9d2b-464">Wygasłe, wyłączone i zaktualizowane wpisy tajne w magazynie `Reload` kluczy nie są przestrzegane przez aplikację, dopóki nie zostanie wykonana.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a9d2b-465">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="a9d2b-465">Disabled and expired secrets</span></span>

<span data-ttu-id="a9d2b-466">Wyłączone i wygasłe <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>wpisy tajne rzucać .</span><span class="sxs-lookup"><span data-stu-id="a9d2b-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a9d2b-467">Aby zapobiec zrzucaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj wyłączony lub wygasły klucz tajny.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a9d2b-468">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="a9d2b-468">Troubleshoot</span></span>

<span data-ttu-id="a9d2b-469">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywany w [ASP.NET infrastruktury rejestrowania rdzenia](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a9d2b-470">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a9d2b-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a9d2b-471">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w usłudze Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a9d2b-472">Magazyn kluczy nie istnieje w usłudze Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a9d2b-473">Aplikacja nie jest autoryzowana do uzyskiwania dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a9d2b-474">Zasady dostępu nie obejmują `Get` `List` i uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a9d2b-475">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są niepoprawnie nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a9d2b-476">Aplikacja ma nieprawidłową nazwę`KeyVaultName`magazynu kluczy (`AzureADApplicationId`), identyfikator aplikacji usługi`AzureADCertThumbprint`Azure AD ( lub odcisk palca certyfikatu usługi Azure AD ( ).</span><span class="sxs-lookup"><span data-stu-id="a9d2b-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a9d2b-477">Klucz konfiguracji (nazwa) jest niepoprawna w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="a9d2b-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a9d2b-478">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy została utworzona zasada, ale przycisk **Zapisz** nie został wybrany w interfejsie użytkownika **zasad dostępu.**</span><span class="sxs-lookup"><span data-stu-id="a9d2b-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9d2b-479">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a9d2b-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a9d2b-480">Microsoft Azure: Przechowalnia kluczy</span><span class="sxs-lookup"><span data-stu-id="a9d2b-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a9d2b-481">Microsoft Azure: dokumentacja magazynu kluczy</span><span class="sxs-lookup"><span data-stu-id="a9d2b-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a9d2b-482">Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a9d2b-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a9d2b-483">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="a9d2b-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a9d2b-484">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="a9d2b-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a9d2b-485">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="a9d2b-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

