---
title: Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core
author: rick-anderson
description: Informacje dotyczące konfigurowania aplikacji przy użyciu par nazwa-wartość ładowanych w czasie wykonywania przy użyciu dostawcy konfiguracji Azure Key Vault.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 20561b2608b343d0c0bcf545cc9c48d1886b7cb9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022020"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault dostawcę konfiguracji w programie ASP.NET Core

Według [Andrew Stanton-pielęgniarki](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych. Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi. Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:

* Kontrolowanie dostępu do poufnych danych konfiguracyjnych.
* Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakiety

Dodaj odwołanie do pakietu do [Microsoft.Extensions.Configwersja. Pakiet AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Przykładowa aplikacja

Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :

* `Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault. Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.
* `Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji. Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta). `Managed`Wersja przykładu musi zostać wdrożona na platformie Azure. Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .

Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Magazyn tajny w środowisku programistycznym

Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets). Gdy aplikacja Przykładowa zostanie uruchomiona na komputerze lokalnym w środowisku deweloperskim, wpisy tajne są ładowane z lokalnego magazynu lokalnych wpisów tajnych.

Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji. Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Wpisy tajne są tworzone jako pary nazwa-wartość. Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .

Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` . Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Magazyn tajny w środowisku produkcyjnym z Azure Key Vault

Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację. Więcej informacji można znaleźć w temacie.

1. Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):

   * Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.
   * Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .
   * Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.

   Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).

1. Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.

1. Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.

   Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek. Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek). Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy. W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.

   Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją. Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika. Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure

Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**. Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure. Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.

Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .

1. Utwórz certyfikat archiwum PKCS # 12 (*PFX*). Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).
1. Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika. Oznaczenie klucza jako możliwego do eksportu jest opcjonalne. Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.
1. Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).
1. Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).
1. Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:
   1. Wybierz aplikację w usłudze Azure AD.
   1. Przejdź do **przystawki certyfikaty & wpisy tajne**.
   1. Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny. Akceptowany jest certyfikat *CER*, *PEM*lub *CRT* .
1. Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *appsettings.jsaplikacji w* pliku.
1. Przejdź do **magazynu kluczy** w Azure Portal.
1. Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.
1. Wybierz pozycję **zasady dostępu**.
1. Wybierz pozycję **Dodaj zasady dostępu**.
1. Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .
1. Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy. Wybierz przycisk **Wybierz** .
1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Zapisz**.
1. Wdróż aplikację.

`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:

* Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .
* Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia. Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certyfikat X. 509 jest zarządzany przez system operacyjny. Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez *appsettings.jsw* pliku:

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Przykładowe wartości:

* Nazwa magazynu kluczy:`contosovault`
* Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`
* Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.js*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego. W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem. W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.

## <a name="use-managed-identities-for-azure-resources"></a>Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure

**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.

Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .

Wprowadź nazwę magazynu do *appsettings.jsaplikacji w* pliku. Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji. Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *appsettings.js* pliku.

Wdróż przykładową aplikację w Azure App Service.

Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi. Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu. Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.

Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.

Przykładowa aplikacja:

* Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia. Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Przykładowa wartość nazwy magazynu kluczy:`contosovault`
    
*appsettings.js*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego. W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika. W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.

Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy. Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.

Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Opcje konfiguracji

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>może akceptować <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Właściwość         | Opis |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>służy do pobierania wartości. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>wystąpienie używane do kontrolowania ładowania klucza tajnego. |
| `ReloadInterval` | `Timespan`aby poczekać między kolejnymi próbami sondowania magazynu kluczy pod kątem zmian. Wartość domyślna to `null` (konfiguracja nie jest ponownie ładowana). |
| `Vault`          | Identyfikator URI magazynu kluczy. |

## <a name="use-a-key-name-prefix"></a>Użyj prefiksu nazwy klucza

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji. Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji. Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.

> [!WARNING]
> Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie. Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.

W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy). Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji. W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` . Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:

* `Load`ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu. Inne wpisy tajne nie są ładowane.
* `GetKey`:
  * Usuwa prefiks z nazwy wpisu tajnego.
  * Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek). Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji. Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy. Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.

Gdy takie podejście jest zaimplementowane:

1. Wersja aplikacji określona w pliku projektu aplikacji. W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy. Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).

1. Wersja `5000` (z kreską) jest usuwana z nazwy klucza. W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.

1. Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.

> [!NOTE]
> Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.

## <a name="bind-an-array-to-a-class"></a>Powiąż tablicę z klasą

Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.

W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ). Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klucze Azure Key Vault nie mogą używać dwukropka jako separatora. Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji). Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).

Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON. W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia*Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:

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

Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:

| Klucz | Wartość |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Załaduj ponownie klucze tajne

Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania. Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Wyłączone i wygasłe wpisy tajne

Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index). Następujące warunki uniemożliwią ładowanie konfiguracji:

* Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.
* Magazyn kluczy nie istnieje w Azure Key Vault.
* Aplikacja nie ma uprawnień dostępu do magazynu kluczy.
* Zasady dostępu nie obejmują `Get` i `List` uprawnień.
* W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.
* Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).
* Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.
* Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: dokumentacja Key Vault](/azure/key-vault/)
* [Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Klasa KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET](/azure/key-vault/quick-create-net)
* [Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym dokumencie wyjaśniono, jak za pomocą dostawcy konfiguracji [Key Vault Microsoft Azure](https://azure.microsoft.com/services/key-vault/) załadować wartości konfiguracji aplikacji z Azure Key Vault wpisów tajnych. Azure Key Vault to usługa oparta na chmurze, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi. Typowe scenariusze używania Azure Key Vault z aplikacjami ASP.NET Core obejmują:

* Kontrolowanie dostępu do poufnych danych konfiguracyjnych.
* Spełnienie wymagania dotyczącego sprawdzania poprawności sprzętowych modułów zabezpieczeń FIPS 140-2 Level 2 (HSM) podczas przechowywania danych konfiguracyjnych.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakiety

Dodaj odwołanie do pakietu do [Microsoft.Extensions.Configwersja. Pakiet AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Przykładowa aplikacja

Przykładowa aplikacja działa w dwóch trybów określonych przez `#define` instrukcję w górnej części pliku *program.cs* :

* `Certificate`: Ilustruje użycie identyfikatora klienta Azure Key Vault i certyfikatu X. 509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w Azure Key Vault. Tę wersję przykładu można uruchomić z dowolnej lokalizacji wdrożonej do Azure App Service lub dowolnego hosta, który może obsługiwać aplikację ASP.NET Core.
* `Managed`: Pokazuje, jak używać [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania aplikacji do Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji. Podczas uwierzytelniania przy użyciu tożsamości zarządzanych nie są wymagane identyfikatory aplikacji i hasła usługi Azure AD (klucz tajny klienta). `Managed`Wersja przykładu musi zostać wdrożona na platformie Azure. Postępuj zgodnie ze wskazówkami zawartymi w sekcji [Korzystanie z zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) .

Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji przy użyciu dyrektyw preprocesora ( `#define` ), zobacz <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Magazyn tajny w środowisku programistycznym

Ustaw wpisy tajne lokalnie przy użyciu [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets). Gdy aplikacja Przykładowa zostanie uruchomiona na komputerze lokalnym w środowisku deweloperskim, wpisy tajne są ładowane z lokalnego magazynu lokalnych wpisów tajnych.

Narzędzie Secret Manager wymaga `<UserSecretsId>` właściwości w pliku projektu aplikacji. Ustaw wartość właściwości ( `{GUID}` ) na dowolny unikatowy identyfikator GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Wpisy tajne są tworzone jako pary nazwa-wartość. Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w nazwach kluczy [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) .

Menedżer wpisów tajnych jest używany z poziomu powłoki poleceń otwartej w [katalogu głównym zawartości](xref:fundamentals/index#content-root)projektu, gdzie `{SECRET NAME}` jest nazwą i `{SECRET VALUE}` jest wartością:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Wykonaj następujące polecenia w powłoce poleceń z poziomu [głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Jeśli te wpisy tajne są przechowywane w Azure Key Vault w [magazynie kluczy tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault, `_dev` sufiks zostanie zmieniony na `_prod` . Sufiks udostępnia wizualizację wizualną w danych wyjściowych aplikacji wskazującej źródło wartości konfiguracyjnych.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Magazyn tajny w środowisku produkcyjnym z Azure Key Vault

Instrukcje dostępne w [przewodniku szybki start: Ustawianie i pobieranie wpisu tajnego z Azure Key Vault za pomocą interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli) są zestawione w tym miejscu na potrzeby tworzenia Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację. Więcej informacji można znaleźć w temacie.

1. Otwórz usługę Azure Cloud Shell przy użyciu jednej z następujących metod w [Azure Portal](https://portal.azure.com/):

   * Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Użyj ciągu wyszukiwania "interfejs wiersza polecenia platformy Azure" w polu tekstowym.
   * Otwórz Cloud Shell w przeglądarce za pomocą przycisku **uruchom Cloud Shell** .
   * Wybierz przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal.

   Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure](/cli/azure/) i [Omówienie Azure Cloud Shell](/azure/cloud-shell/overview).

1. Jeśli nie masz jeszcze uwierzytelnienia, zaloguj się za pomocą `az login` polecenia.

1. Utwórz grupę zasobów za pomocą następującego polecenia, gdzie `{RESOURCE GROUP NAME}` jest nazwą grupy zasobów dla nowej grupy zasobów i `{LOCATION}` jest regionem platformy Azure (centrum danych):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Utwórz magazyn kluczy w grupie zasobów przy użyciu następującego polecenia, gdzie `{KEY VAULT NAME}` jest nazwą nowego magazynu kluczy i `{LOCATION}` jest regionem platformy Azure (centrum danych):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Utwórz klucze tajne w magazynie kluczy jako pary nazwa-wartość.

   Nazwy tajne Azure Key Vault są ograniczone do znaków alfanumerycznych i kresek. Wartości hierarchiczne (sekcje konfiguracji) używają `--` jako separatora (dwóch kresek). Dwukropek, które zwykle są używane do ograniczania sekcji z podklucza w [konfiguracji ASP.NET Core](xref:fundamentals/configuration/index), nie są dozwolone w nazwach tajnych magazynu kluczy. W związku z tym dwie kreski są używane i zamieniane na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.

   Następujące wpisy tajne są przeznaczone do użycia z przykładową aplikacją. Wartości obejmują sufiks, `_prod` Aby odróżnić je od `_dev` wartości sufiksów ładowanych w środowisku programistycznym z kluczy tajnych użytkownika. Zamień `{KEY VAULT NAME}` na nazwę magazynu kluczy utworzonego w poprzednim kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Używanie identyfikatora aplikacji i certyfikatu X. 509 dla aplikacji nieobsługiwanych przez platformę Azure

Skonfiguruj usługę Azure AD, Azure Key Vault i aplikację, aby używać identyfikatora aplikacji Azure Active Directory i certyfikatu X. 509 do uwierzytelniania w magazynie kluczy **, gdy aplikacja jest hostowana poza platformą Azure**. Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Chociaż użycie identyfikatora aplikacji i certyfikatu X. 509 jest obsługiwane w przypadku aplikacji hostowanych na platformie Azure, zalecamy używanie [zarządzanych tożsamości dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure. Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji ani w środowisku deweloperskim.

Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X. 509, gdy `#define` instrukcja w górnej części pliku *program.cs* jest ustawiona na `Certificate` .

1. Utwórz certyfikat archiwum PKCS # 12 (*PFX*). Opcje tworzenia certyfikatów obejmują [MakeCert w systemach Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).
1. Zainstaluj certyfikat w osobistym magazynie certyfikatów bieżącego użytkownika. Oznaczenie klucza jako możliwego do eksportu jest opcjonalne. Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.
1. Wyeksportuj certyfikat archiwum PKCS # 12 (*PFX*) jako certyfikat szyfrowany algorytmem DER (*CER*).
1. Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).
1. Przekaż certyfikat szyfrowany algorytmem DER (*CER*) do usługi Azure AD:
   1. Wybierz aplikację w usłudze Azure AD.
   1. Przejdź do **przystawki certyfikaty & wpisy tajne**.
   1. Wybierz pozycję **Przekaż certyfikat** , aby przekazać certyfikat zawierający klucz publiczny. Akceptowany jest certyfikat *CER*, *PEM*lub *CRT* .
1. Zapisz nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu w *appsettings.jsaplikacji w* pliku.
1. Przejdź do **magazynu kluczy** w Azure Portal.
1. Wybierz magazyn kluczy utworzony w [magazynie wpisów tajnych w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Azure Key Vault.
1. Wybierz pozycję **zasady dostępu**.
1. Wybierz pozycję **Dodaj zasady dostępu**.
1. Otwórz **uprawnienia do wpisów tajnych** i Udostępnij aplikację z uprawnieniami **pobierania** i **wyświetlania listy** .
1. Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz zarejestrowaną aplikację według nazwy. Wybierz przycisk **Wybierz** .
1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Zapisz**.
1. Wdróż aplikację.

`Certificate`Przykładowa aplikacja uzyskuje swoje wartości konfiguracji z `IConfigurationRoot` tą samą nazwą jak nazwa wpisu tajnego:

* Wartości niehierarchiczne: wartość dla `SecretName` jest uzyskiwana z `config["SecretName"]` .
* Wartości hierarchiczne (sekcje): Użyj `:` zapisu (dwukropek) lub `GetSection` metody rozszerzenia. Użyj jednego z tych metod, aby uzyskać wartość konfiguracji:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certyfikat X. 509 jest zarządzany przez system operacyjny. Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wartości dostarczone przez *appsettings.jsw* pliku:

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Przykładowe wartości:

* Nazwa magazynu kluczy:`contosovault`
* Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`
* Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.js*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego. W środowisku programistycznym wartości klucza tajnego są ładowane z `_dev` sufiksem. W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem.

## <a name="use-managed-identities-for-azure-resources"></a>Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure

**Aplikacja wdrożona na platformie Azure** może korzystać z [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview), co pozwala na uwierzytelnianie aplikacji przy użyciu Azure Key Vault uwierzytelniania usługi Azure AD bez poświadczeń (Identyfikator aplikacji i hasło/klucz tajny klienta) przechowywane w aplikacji.

Przykładowa aplikacja używa zarządzanych tożsamości dla zasobów platformy Azure, gdy w `#define` górnej części pliku *program.cs* jest ustawiona wartość `Managed` .

Wprowadź nazwę magazynu do *appsettings.jsaplikacji w* pliku. Aplikacja Przykładowa nie wymaga identyfikatora aplikacji ani hasła (klucza tajnego klienta) w przypadku ustawienia `Managed` wersji, więc można zignorować te wpisy konfiguracji. Aplikacja została wdrożona na platformie Azure, a platforma Azure uwierzytelnia aplikację w celu uzyskiwania dostępu do Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w *appsettings.js* pliku.

Wdróż przykładową aplikację w Azure App Service.

Aplikacja wdrożona do Azure App Service jest automatycznie zarejestrowana w usłudze Azure AD podczas tworzenia usługi. Uzyskaj identyfikator obiektu z wdrożenia do użycia w poniższym poleceniu. Identyfikator obiektu jest pokazywany w Azure Portal na **Identity** panelu App Service.

Korzystając z interfejsu wiersza polecenia platformy Azure i identyfikatora obiektu aplikacji, Udostępnij aplikację `list` i `get` uprawnienia dostępu do magazynu kluczy:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Uruchom ponownie aplikację** przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal.

Przykładowa aplikacja:

* Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez parametrów połączenia. Jeśli nie podano parametrów połączenia, Dostawca próbuje uzyskać token dostępu z zarządzanych tożsamości dla zasobów platformy Azure.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Zostanie utworzony nowy z `AzureServiceTokenProvider` wywołaniem zwrotnym tokenu wystąpienia.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Wystąpienie jest używane z domyślną implementacją programu <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która ładuje wszystkie wartości tajne i zastępuje podwójne myślniki ( `--` ) średnikami ( `:` ) w nazwach kluczy.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Przykładowa wartość nazwy magazynu kluczy:`contosovault`
    
*appsettings.js*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości klucza tajnego. W środowisku programistycznym wartości klucza tajnego mają `_dev` sufiks, ponieważ są one dostarczane przez klucze tajne użytkownika. W środowisku produkcyjnym wartości są ładowane z `_prod` sufiksem, ponieważ są one udostępniane przez Azure Key Vault.

Jeśli `Access denied` wystąpi błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i że masz dostęp do magazynu kluczy. Upewnij się, że usługa została uruchomiona ponownie na platformie Azure.

Aby uzyskać informacje na temat używania dostawcy z zarządzaną tożsamością i potoku usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Azure Resource Manager z maszyną wirtualną przy użyciu tożsamości usługi zarządzanej](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Użyj prefiksu nazwy klucza

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia Przeciążenie, które akceptuje implementację <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , która pozwala na kontrolowanie sposobu, w jaki wpisy tajne magazynu kluczy są konwertowane na klucze konfiguracji. Na przykład można zaimplementować interfejs w celu załadowania wartości tajnych na podstawie wartości prefiksu podanej podczas uruchamiania aplikacji. Dzięki temu można na przykład ładować wpisy tajne na podstawie wersji aplikacji.

> [!WARNING]
> Nie należy używać prefiksów w kluczach tajnych magazynu kluczy w celu umieszczenia wpisów tajnych dla wielu aplikacji w tym samym magazynie kluczy lub umieszczenia tajemnicy środowiskowej (na przykład *tworzenia* i *produkcji* wpisów tajnych) w tym samym magazynie. Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperskie i produkcyjne używały oddzielnych magazynów kluczy do izolowania środowisk aplikacji w celu uzyskania najwyższego poziomu zabezpieczeń.

W poniższym przykładzie wpis tajny jest ustanowiony w magazynie kluczy (oraz za pomocą narzędzia tajnego Menedżera dla środowiska programistycznego) `5000-AppSecret` (okresy nie są dozwolone w nazwach wpisów tajnych magazynu kluczy). Ten klucz tajny reprezentuje wpis tajny aplikacji dla 5.0.0.0 wersji aplikacji. W przypadku innej wersji aplikacji 5.1.0.0 wpis tajny jest dodawany do magazynu kluczy (i przy użyciu narzędzia do zarządzania kluczami tajnymi) dla programu `5100-AppSecret` . Każda wersja aplikacji ładuje wartość tajnej wersji do swojej konfiguracji jako `AppSecret` , oddzielając ją od wersji podczas ładowania klucza tajnego.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>jest wywoływana z niestandardowym <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementacja reaguje na prefiksy wersji wpisów tajnych w celu załadowania odpowiedniego wpisu tajnego do konfiguracji:

* `Load`ładuje wpis tajny, gdy jego nazwa zaczyna się od prefiksu. Inne wpisy tajne nie są ładowane.
* `GetKey`:
  * Usuwa prefiks z nazwy wpisu tajnego.
  * Zastępuje dwie kreski w dowolnej nazwie znakiem `KeyDelimiter` , który jest ogranicznikiem używanym w konfiguracji (zazwyczaj dwukropek). Azure Key Vault nie zezwala na dwukropek w nazwach kluczy tajnych.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`Metoda jest wywoływana przez algorytm dostawcy, który wykonuje iterację przez wpisy tajne magazynu, aby znaleźć te, które mają prefiks wersji. Po znalezieniu prefiksu wersji w programie `Load` algorytm używa `GetKey` metody do zwrócenia nazwy konfiguracji tajnej nazwy. Rozdziela prefiks wersji z nazwy wpisu tajnego i zwraca resztę nazwy wpisu tajnego do załadowania do par nazwa-wartość konfiguracji aplikacji.

Gdy takie podejście jest zaimplementowane:

1. Wersja aplikacji określona w pliku projektu aplikacji. W poniższym przykładzie wersja aplikacji jest ustawiona na `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Upewnij się, że `<UserSecretsId>` Właściwość jest obecna w pliku projektu aplikacji, gdzie `{GUID}` jest identyfikatorem GUID dostarczonym przez użytkownika:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Zapisz następujące wpisy tajne lokalnie za pomocą [Narzędzia tajnego Menedżera](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Wpisy tajne są zapisywane w Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy. Wpis tajny ciągu dla `5000-AppSecret` jest zgodny z wersją aplikacji określoną w pliku projektu aplikacji ( `5.0.0.0` ).

1. Wersja `5000` (z kreską) jest usuwana z nazwy klucza. W całej aplikacji odczytywanie konfiguracji przy użyciu klucza powoduje `AppSecret` załadowanie wartości klucza tajnego.

1. Jeśli wersja aplikacji została zmieniona w pliku projektu na, `5.1.0.0` a aplikacja zostanie uruchomiona ponownie, zwracana wartość wpisu tajnego jest `5.1.0.0_secret_value_dev` w środowisku deweloperskim i `5.1.0.0_secret_value_prod` w produkcji.

> [!NOTE]
> Możesz również wprowadzić własną <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementację programu <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Niestandardowy klient umożliwia udostępnianie pojedynczego wystąpienia klienta w aplikacji.

## <a name="bind-an-array-to-a-class"></a>Powiąż tablicę z klasą

Dostawca może odczytać wartości konfiguracyjne w tablicy w celu powiązania z tablicą POCO.

W przypadku odczytywania ze źródła konfiguracji, które pozwala na używanie kluczy zawierających dwukropek ( `:` ), segment klucza numerycznego służy do odróżniania kluczy tworzących tablicę ( `:0:` , `:1:` , &hellip; `:{n}:` ). Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klucze Azure Key Vault nie mogą używać dwukropka jako separatora. Metoda opisana w tym temacie używa podwójnych kresek ( `--` ) jako separatora wartości hierarchicznych (sekcji). Klucze tablic są przechowywane w Azure Key Vault przy użyciu podwójnych kresek i segmentów kluczy numerycznych ( `--0--` , `--1--` , &hellip; `--{n}--` ).

Zapoznaj się z następującą konfiguracją dostawcy rejestrowania [Serilog](https://serilog.net/) dostarczoną przez plik JSON. W tablicy są zdefiniowane dwa literały obiektów, `WriteTo` które odzwierciedlają dwa *ujścia*Serilog, które opisują miejsca docelowe na potrzeby rejestrowania danych wyjściowych:

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

Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana w Azure Key Vault przy użyciu notacji podwójnej kreski ( `--` ) i segmentów liczbowych:

| Klucz | Wartość |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Załaduj ponownie klucze tajne

Wpisy tajne są buforowane do momentu `IConfigurationRoot.Reload()` wywołania. Wygasłe, wyłączone i zaktualizowane klucze tajne w magazynie kluczy nie są przestrzegane przez aplikację do momentu `Reload` wykonania.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Wyłączone i wygasłe wpisy tajne

Wyłączone i wygasłe wpisy tajne generują <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Aby zapobiec zgłaszaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj klucz tajny wyłączony lub wygasły.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywana do [infrastruktury rejestrowania ASP.NET Core](xref:fundamentals/logging/index). Następujące warunki uniemożliwią ładowanie konfiguracji:

* Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w Azure Active Directory.
* Magazyn kluczy nie istnieje w Azure Key Vault.
* Aplikacja nie ma uprawnień dostępu do magazynu kluczy.
* Zasady dostępu nie obejmują `Get` i `List` uprawnień.
* W magazynie kluczy dane konfiguracji (para nazwa-wartość) są nieprawidłowo nazwane, brakujące, wyłączone lub wygasłe.
* Aplikacja ma nieprawidłową nazwę magazynu kluczy ( `KeyVaultName` ), identyfikator aplikacji usługi Azure AD ( `AzureADApplicationId` ) lub odcisk palca certyfikatu usługi Azure AD ( `AzureADCertThumbprint` ).
* Klucz konfiguracji (nazwa) jest niepoprawny w aplikacji dla wartości, którą próbujesz załadować.
* Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy zasady zostały utworzone, ale nie wybrano przycisku **Zapisz** w interfejsie użytkownika **zasad dostępu** .

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: dokumentacja Key Vault](/azure/key-vault/)
* [Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Klasa KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET](/azure/key-vault/quick-create-net)
* [Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

