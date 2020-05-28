---
<span data-ttu-id="54385-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-102">'Blazor'</span></span>
- <span data-ttu-id="54385-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-103">'Identity'</span></span>
- <span data-ttu-id="54385-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-105">'Razor'</span></span>
- <span data-ttu-id="54385-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="54385-107">Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54385-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="54385-108">Według [Andrew Stanton-pielęgniarki](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="54385-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54385-109">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="54385-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="54385-110">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="54385-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="54385-111">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="54385-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="54385-112">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="54385-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="54385-113">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="54385-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="54385-114">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54385-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="54385-115">Pakiety</span><span class="sxs-lookup"><span data-stu-id="54385-115">Packages</span></span>

<span data-ttu-id="54385-116">Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="54385-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="54385-117">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="54385-117">Sample app</span></span>

<span data-ttu-id="54385-118">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="54385-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="54385-119">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="54385-120">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54385-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="54385-121">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="54385-122">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="54385-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="54385-123">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="54385-124">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="54385-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="54385-125">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="54385-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="54385-126">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="54385-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="54385-127">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="54385-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="54385-128">Gdy aplikacja Przykładowa zostanie uruchomiona na komputerze lokalnym w środowisku deweloperskim, wpisy tajne są ładowane z lokalnego magazynu lokalnych wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="54385-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="54385-129">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="54385-130">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="54385-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="54385-131">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="54385-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="54385-132">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="54385-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="54385-133">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="54385-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="54385-134">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="54385-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="54385-135">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="54385-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="54385-136">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="54385-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="54385-137">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="54385-138">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="54385-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="54385-139">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="54385-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="54385-140">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="54385-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="54385-141">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="54385-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="54385-142">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="54385-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="54385-143">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="54385-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="54385-144">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="54385-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="54385-145">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="54385-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="54385-146">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="54385-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="54385-147">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="54385-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="54385-148">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="54385-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="54385-149">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="54385-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="54385-150">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="54385-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="54385-151">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="54385-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="54385-152">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="54385-153">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="54385-154">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="54385-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="54385-155">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="54385-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="54385-156">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="54385-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="54385-157">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="54385-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="54385-158">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**.</span><span class="sxs-lookup"><span data-stu-id="54385-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="54385-159">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="54385-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="54385-160">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="54385-161">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="54385-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="54385-162">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="54385-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="54385-163">Utwórz certyfikat archiwum PKCS # 12 (*PFX*).</span><span class="sxs-lookup"><span data-stu-id="54385-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="54385-164">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="54385-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="54385-165">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="54385-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="54385-166">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="54385-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="54385-167">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="54385-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="54385-168">Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="54385-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="54385-169">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="54385-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="54385-170">Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="54385-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="54385-171">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="54385-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="54385-172">Przejdź do **przystawki certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="54385-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="54385-173">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="54385-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="54385-174">Akceptowany jest certyfikat *CER*, *PEM*lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="54385-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="54385-175">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w pliku *appSettings. JSON* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="54385-176">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="54385-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="54385-177">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="54385-178">Wybierz pozycję **Zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="54385-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="54385-179">Wybierz pozycję **Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="54385-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="54385-180">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="54385-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="54385-181">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="54385-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="54385-182">Wybierz przycisk **Wybierz** .</span><span class="sxs-lookup"><span data-stu-id="54385-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="54385-183">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="54385-183">Select **OK**.</span></span>
1. <span data-ttu-id="54385-184">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="54385-184">Select **Save**.</span></span>
1. <span data-ttu-id="54385-185">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="54385-185">Deploy the app.</span></span>

<span data-ttu-id="54385-186">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="54385-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="54385-187">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="54385-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="54385-188">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="54385-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="54385-189">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="54385-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="54385-190">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="54385-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="54385-191">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="54385-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="54385-192">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="54385-192">Example values:</span></span>

* <span data-ttu-id="54385-193">Nazwa magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="54385-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="54385-194">Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="54385-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="54385-195">Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="54385-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="54385-196">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="54385-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="54385-197">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="54385-198">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="54385-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="54385-199">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="54385-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="54385-200">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="54385-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="54385-201">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="54385-202">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="54385-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="54385-203">Wprowadź nazwę magazynu w pliku *appSettings. JSON* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="54385-204">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="54385-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="54385-205">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="54385-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="54385-206">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="54385-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="54385-207">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="54385-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="54385-208">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="54385-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="54385-209">Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="54385-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="54385-210">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="54385-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="54385-211">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="54385-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="54385-212">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="54385-212">The sample app:</span></span>

* <span data-ttu-id="54385-213">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="54385-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="54385-214">Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="54385-215"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="54385-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="54385-216"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="54385-217">Przykładowa wartość nazwy magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="54385-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="54385-218">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="54385-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="54385-219">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="54385-220">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="54385-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="54385-221">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="54385-222">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="54385-223">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="54385-224">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="54385-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="54385-225">Opcje konfiguracji</span><span class="sxs-lookup"><span data-stu-id="54385-225">Configuration options</span></span>

<span data-ttu-id="54385-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>może akceptować <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="54385-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="54385-227">Właściwość</span><span class="sxs-lookup"><span data-stu-id="54385-227">Property</span></span>         | <span data-ttu-id="54385-228">Opis</span><span class="sxs-lookup"><span data-stu-id="54385-228">Description</span></span> |
| ---
<span data-ttu-id="54385-229">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-230">'Blazor'</span></span>
- <span data-ttu-id="54385-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-231">'Identity'</span></span>
- <span data-ttu-id="54385-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-233">'Razor'</span></span>
- <span data-ttu-id="54385-234">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-235">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-236">'Blazor'</span></span>
- <span data-ttu-id="54385-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-237">'Identity'</span></span>
- <span data-ttu-id="54385-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-239">'Razor'</span></span>
- <span data-ttu-id="54385-240">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-241">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-242">'Blazor'</span></span>
- <span data-ttu-id="54385-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-243">'Identity'</span></span>
- <span data-ttu-id="54385-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-245">'Razor'</span></span>
- <span data-ttu-id="54385-246">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-247">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-248">'Blazor'</span></span>
- <span data-ttu-id="54385-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-249">'Identity'</span></span>
- <span data-ttu-id="54385-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-251">'Razor'</span></span>
- <span data-ttu-id="54385-252">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-253">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-254">'Blazor'</span></span>
- <span data-ttu-id="54385-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-255">'Identity'</span></span>
- <span data-ttu-id="54385-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-257">'Razor'</span></span>
- <span data-ttu-id="54385-258">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-259">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-260">'Blazor'</span></span>
- <span data-ttu-id="54385-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-261">'Identity'</span></span>
- <span data-ttu-id="54385-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-263">'Razor'</span></span>
- <span data-ttu-id="54385-264">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-264">'SignalR' uid:</span></span> 

<span data-ttu-id="54385-265">-------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-266">'Blazor'</span></span>
- <span data-ttu-id="54385-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-267">'Identity'</span></span>
- <span data-ttu-id="54385-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-269">'Razor'</span></span>
- <span data-ttu-id="54385-270">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-271">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-272">'Blazor'</span></span>
- <span data-ttu-id="54385-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-273">'Identity'</span></span>
- <span data-ttu-id="54385-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-275">'Razor'</span></span>
- <span data-ttu-id="54385-276">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="54385-277">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="54385-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="54385-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="54385-278">'Blazor'</span></span>
- <span data-ttu-id="54385-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="54385-279">'Identity'</span></span>
- <span data-ttu-id="54385-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="54385-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="54385-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="54385-281">'Razor'</span></span>
- <span data-ttu-id="54385-282">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="54385-282">'SignalR' uid:</span></span> 

<span data-ttu-id="54385-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> służy do pobierania wartości.</span><span class="sxs-lookup"><span data-stu-id="54385-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="54385-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> wystąpienie używane do kontrolowania ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="54385-285">| | `ReloadInterval` | `Timespan` aby poczekać między kolejnymi próbami sondowania magazynu kluczy pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="54385-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="54385-286">Wartość domyślna to `null` (konfiguracja nie jest ponownie ładowana).</span><span class="sxs-lookup"><span data-stu-id="54385-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="54385-287">| | `Vault`          | Identyfikator URI magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="54385-288">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="54385-288">Use a key name prefix</span></span>

<span data-ttu-id="54385-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="54385-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="54385-290">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="54385-291">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="54385-292">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="54385-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="54385-293">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="54385-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="54385-294">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="54385-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="54385-295">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="54385-296">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="54385-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="54385-297">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="54385-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="54385-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="54385-299"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="54385-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="54385-300">`Load`ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="54385-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="54385-301">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="54385-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="54385-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="54385-302">`GetKey`:</span></span>
  * <span data-ttu-id="54385-303">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="54385-304">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="54385-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="54385-305">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="54385-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="54385-306">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="54385-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="54385-307">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="54385-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="54385-308">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="54385-309">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="54385-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="54385-310">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="54385-311">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="54385-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="54385-312">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="54385-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="54385-313">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="54385-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="54385-314">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="54385-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="54385-315">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="54385-316">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="54385-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="54385-317">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="54385-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="54385-318">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="54385-319">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="54385-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="54385-320">Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="54385-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="54385-321">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="54385-322">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="54385-322">Bind an array to a class</span></span>

<span data-ttu-id="54385-323">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="54385-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="54385-324">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="54385-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="54385-325">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="54385-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="54385-326">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="54385-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="54385-327">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="54385-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="54385-328">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="54385-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="54385-329">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="54385-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="54385-330">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia*Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="54385-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="54385-331">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="54385-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="54385-332">Klucz</span><span class="sxs-lookup"><span data-stu-id="54385-332">Key</span></span> | <span data-ttu-id="54385-333">Wartość</span><span class="sxs-lookup"><span data-stu-id="54385-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="54385-334">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="54385-334">Reload secrets</span></span>

<span data-ttu-id="54385-335">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="54385-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="54385-336">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="54385-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="54385-337">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="54385-337">Disabled and expired secrets</span></span>

<span data-ttu-id="54385-338">Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="54385-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="54385-339">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="54385-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="54385-340">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="54385-340">Troubleshoot</span></span>

<span data-ttu-id="54385-341">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="54385-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="54385-342">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="54385-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="54385-343">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="54385-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="54385-344">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="54385-345">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="54385-346">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="54385-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="54385-347">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="54385-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="54385-348">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="54385-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="54385-349">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="54385-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="54385-350">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="54385-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54385-351">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="54385-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="54385-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="54385-353">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="54385-354">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="54385-355">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="54385-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="54385-356">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="54385-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="54385-357">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="54385-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54385-358">W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="54385-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="54385-359">Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi.</span><span class="sxs-lookup"><span data-stu-id="54385-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="54385-360">Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:</span><span class="sxs-lookup"><span data-stu-id="54385-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="54385-361">Kontrolowanie dostępu do poufnych danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="54385-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="54385-362">Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="54385-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="54385-363">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54385-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="54385-364">Pakiety</span><span class="sxs-lookup"><span data-stu-id="54385-364">Packages</span></span>

<span data-ttu-id="54385-365">Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="54385-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="54385-366">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="54385-366">Sample app</span></span>

<span data-ttu-id="54385-367">Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="54385-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="54385-368">`Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="54385-369">Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54385-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="54385-370">`Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="54385-371">Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta).</span><span class="sxs-lookup"><span data-stu-id="54385-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="54385-372">`Managed`Wersja przykładu musi zostać wdrożona na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="54385-373">Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="54385-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="54385-374">Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="54385-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="54385-375">Magazyn tajny w środowisku programistycznym</span><span class="sxs-lookup"><span data-stu-id="54385-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="54385-376">Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="54385-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="54385-377">Gdy aplikacja Przykładowa zostanie uruchomiona na komputerze lokalnym w środowisku deweloperskim, wpisy tajne są ładowane z lokalnego magazynu lokalnych wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="54385-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="54385-378">Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="54385-379">Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:</span><span class="sxs-lookup"><span data-stu-id="54385-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="54385-380">Wpisy tajne są tworzone jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="54385-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="54385-381">Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="54385-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="54385-382">Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:</span><span class="sxs-lookup"><span data-stu-id="54385-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="54385-383">Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="54385-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="54385-384">Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="54385-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="54385-385">Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="54385-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="54385-386">Magazyn tajny w środowisku produkcyjnym z Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="54385-387">Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="54385-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="54385-388">Więcej informacji można znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="54385-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="54385-389">Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="54385-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="54385-390">Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="54385-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="54385-391">Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="54385-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="54385-392">Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="54385-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="54385-393">Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="54385-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="54385-394">Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="54385-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="54385-395">Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.</span><span class="sxs-lookup"><span data-stu-id="54385-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="54385-396">Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="54385-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="54385-397">Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):</span><span class="sxs-lookup"><span data-stu-id="54385-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="54385-398">Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="54385-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="54385-399">Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek.</span><span class="sxs-lookup"><span data-stu-id="54385-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="54385-400">Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek).</span><span class="sxs-lookup"><span data-stu-id="54385-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="54385-401">Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="54385-402">W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="54385-403">Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją.</span><span class="sxs-lookup"><span data-stu-id="54385-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="54385-404">Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="54385-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="54385-405">Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:</span><span class="sxs-lookup"><span data-stu-id="54385-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="54385-406">Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure</span><span class="sxs-lookup"><span data-stu-id="54385-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="54385-407">Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**.</span><span class="sxs-lookup"><span data-stu-id="54385-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="54385-408">Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="54385-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="54385-409">Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="54385-410">Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="54385-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="54385-411">Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="54385-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="54385-412">Utwórz certyfikat archiwum PKCS # 12 (*PFX*).</span><span class="sxs-lookup"><span data-stu-id="54385-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="54385-413">Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="54385-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="54385-414">Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="54385-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="54385-415">Oznaczenie klucza jako możliwego do eksportu jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="54385-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="54385-416">Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.</span><span class="sxs-lookup"><span data-stu-id="54385-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="54385-417">Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="54385-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="54385-418">Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).</span><span class="sxs-lookup"><span data-stu-id="54385-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="54385-419">Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:</span><span class="sxs-lookup"><span data-stu-id="54385-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="54385-420">Wybierz aplikację w usłudze Azure AD.</span><span class="sxs-lookup"><span data-stu-id="54385-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="54385-421">Przejdź do **przystawki certyfikaty & wpisy tajne**.</span><span class="sxs-lookup"><span data-stu-id="54385-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="54385-422">Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny.</span><span class="sxs-lookup"><span data-stu-id="54385-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="54385-423">Akceptowany jest certyfikat *CER*, *PEM*lub *CRT* .</span><span class="sxs-lookup"><span data-stu-id="54385-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="54385-424">Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w pliku *appSettings. JSON* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="54385-425">Przejdź do **magazynu kluczy** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="54385-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="54385-426">Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="54385-427">Wybierz pozycję **Zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="54385-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="54385-428">Wybierz pozycję **Dodaj zasady dostępu**.</span><span class="sxs-lookup"><span data-stu-id="54385-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="54385-429">Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .</span><span class="sxs-lookup"><span data-stu-id="54385-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="54385-430">Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy.</span><span class="sxs-lookup"><span data-stu-id="54385-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="54385-431">Wybierz przycisk **Wybierz** .</span><span class="sxs-lookup"><span data-stu-id="54385-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="54385-432">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="54385-432">Select **OK**.</span></span>
1. <span data-ttu-id="54385-433">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="54385-433">Select **Save**.</span></span>
1. <span data-ttu-id="54385-434">Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="54385-434">Deploy the app.</span></span>

<span data-ttu-id="54385-435">`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:</span><span class="sxs-lookup"><span data-stu-id="54385-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="54385-436">Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="54385-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="54385-437">Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="54385-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="54385-438">Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="54385-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="54385-439">Certyfikat X. 509 jest zarządzany przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="54385-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="54385-440">Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez plik *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="54385-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="54385-441">Przykładowe wartości:</span><span class="sxs-lookup"><span data-stu-id="54385-441">Example values:</span></span>

* <span data-ttu-id="54385-442">Nazwa magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="54385-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="54385-443">Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="54385-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="54385-444">Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="54385-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="54385-445">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="54385-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="54385-446">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="54385-447">W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="54385-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="54385-448">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.</span><span class="sxs-lookup"><span data-stu-id="54385-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="54385-449">Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="54385-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="54385-450">**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="54385-451">Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .</span><span class="sxs-lookup"><span data-stu-id="54385-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="54385-452">Wprowadź nazwę magazynu w pliku *appSettings. JSON* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="54385-453">Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="54385-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="54385-454">Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w pliku *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="54385-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="54385-455">Wdróż przykładową aplikację w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="54385-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="54385-456">Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="54385-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="54385-457">Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu.</span><span class="sxs-lookup"><span data-stu-id="54385-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="54385-458">Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="54385-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="54385-459">Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:</span><span class="sxs-lookup"><span data-stu-id="54385-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="54385-460">**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="54385-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="54385-461">Przykładowa aplikacja:</span><span class="sxs-lookup"><span data-stu-id="54385-461">The sample app:</span></span>

* <span data-ttu-id="54385-462">Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="54385-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="54385-463">Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="54385-464"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="54385-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="54385-465"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="54385-466">Przykładowa wartość nazwy magazynu kluczy:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="54385-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="54385-467">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="54385-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="54385-468">Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="54385-469">W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika.</span><span class="sxs-lookup"><span data-stu-id="54385-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="54385-470">W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="54385-471">Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="54385-472">Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="54385-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="54385-473">Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="54385-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="54385-474">Użyj prefiksu nazwy klucza</span><span class="sxs-lookup"><span data-stu-id="54385-474">Use a key name prefix</span></span>

<span data-ttu-id="54385-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="54385-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="54385-476">Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="54385-477">Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="54385-478">Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie.</span><span class="sxs-lookup"><span data-stu-id="54385-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="54385-479">Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="54385-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="54385-480">W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy).</span><span class="sxs-lookup"><span data-stu-id="54385-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="54385-481">Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="54385-482">W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="54385-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="54385-483">Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="54385-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="54385-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="54385-485"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="54385-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="54385-486">`Load`ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu.</span><span class="sxs-lookup"><span data-stu-id="54385-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="54385-487">Inne wpisy tajne nie są ładowane.</span><span class="sxs-lookup"><span data-stu-id="54385-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="54385-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="54385-488">`GetKey`:</span></span>
  * <span data-ttu-id="54385-489">Usuwa prefiks z nazwy wpisu tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="54385-490">Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek).</span><span class="sxs-lookup"><span data-stu-id="54385-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="54385-491">Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.</span><span class="sxs-lookup"><span data-stu-id="54385-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="54385-492">`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji.</span><span class="sxs-lookup"><span data-stu-id="54385-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="54385-493">Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy.</span><span class="sxs-lookup"><span data-stu-id="54385-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="54385-494">Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="54385-495">Gdy takie podejście jest zaimplementowane:</span><span class="sxs-lookup"><span data-stu-id="54385-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="54385-496">Wersja aplikacji określona w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="54385-497">W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="54385-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="54385-498">Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:</span><span class="sxs-lookup"><span data-stu-id="54385-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="54385-499">Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="54385-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="54385-500">Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="54385-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="54385-501">Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="54385-502">Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="54385-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="54385-503">Wersja `5000` (z kreską) jest usuwana z nazwy klucza.</span><span class="sxs-lookup"><span data-stu-id="54385-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="54385-504">W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="54385-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="54385-505">Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.</span><span class="sxs-lookup"><span data-stu-id="54385-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="54385-506">Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="54385-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="54385-507">Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54385-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="54385-508">Powiąż tablicę z klasą</span><span class="sxs-lookup"><span data-stu-id="54385-508">Bind an array to a class</span></span>

<span data-ttu-id="54385-509">Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.</span><span class="sxs-lookup"><span data-stu-id="54385-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="54385-510">W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="54385-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="54385-511">Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="54385-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="54385-512">Klucze Azure Key Vault nie mogą używać dwukropka jako separatora.</span><span class="sxs-lookup"><span data-stu-id="54385-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="54385-513">Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji).</span><span class="sxs-lookup"><span data-stu-id="54385-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="54385-514">Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="54385-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="54385-515">Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON.</span><span class="sxs-lookup"><span data-stu-id="54385-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="54385-516">W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia*Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="54385-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="54385-517">Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:</span><span class="sxs-lookup"><span data-stu-id="54385-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="54385-518">Klucz</span><span class="sxs-lookup"><span data-stu-id="54385-518">Key</span></span> | <span data-ttu-id="54385-519">Wartość</span><span class="sxs-lookup"><span data-stu-id="54385-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="54385-520">Załaduj ponownie klucze tajne</span><span class="sxs-lookup"><span data-stu-id="54385-520">Reload secrets</span></span>

<span data-ttu-id="54385-521">Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania.</span><span class="sxs-lookup"><span data-stu-id="54385-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="54385-522">Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.</span><span class="sxs-lookup"><span data-stu-id="54385-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="54385-523">Wyłączone i wygasłe wpisy tajne</span><span class="sxs-lookup"><span data-stu-id="54385-523">Disabled and expired secrets</span></span>

<span data-ttu-id="54385-524">Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="54385-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="54385-525">Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.</span><span class="sxs-lookup"><span data-stu-id="54385-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="54385-526">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="54385-526">Troubleshoot</span></span>

<span data-ttu-id="54385-527">Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="54385-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="54385-528">Następujące warunki uniemożliwią ładowanie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="54385-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="54385-529">Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="54385-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="54385-530">Magazyn kluczy nie istnieje w Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="54385-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="54385-531">Aplikacja nie ma uprawnień dostępu do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="54385-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="54385-532">Zasady dostępu nie obejmują `Get` i `List` uprawnień.</span><span class="sxs-lookup"><span data-stu-id="54385-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="54385-533">W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.</span><span class="sxs-lookup"><span data-stu-id="54385-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="54385-534">Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="54385-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="54385-535">Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.</span><span class="sxs-lookup"><span data-stu-id="54385-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="54385-536">Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .</span><span class="sxs-lookup"><span data-stu-id="54385-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54385-537">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="54385-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="54385-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="54385-539">Microsoft Azure: dokumentacja Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="54385-540">Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="54385-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="54385-541">Klasa KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="54385-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="54385-542">Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET</span><span class="sxs-lookup"><span data-stu-id="54385-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="54385-543">Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="54385-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

