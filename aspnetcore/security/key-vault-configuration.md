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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Dostawca konfiguracji usługi Azure Key Vault w ASP.NET Core

Przez [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

W tym dokumencie wyjaśniono, jak używać dostawcy konfiguracji [usługi Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do ładowania wartości konfiguracji aplikacji z wpisów tajnych usługi Azure Key Vault. Usługa Azure Key Vault to usługa oparta na chmurze, która pomaga w ochronie kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje i usługi. Typowe scenariusze korzystania z usługi Azure Key Vault z aplikacjami ASP.NET Core obejmują:

* Kontrolowanie dostępu do poufnych danych konfiguracyjnych.
* Spełnienie wymagań dotyczących sprawdzonych modułów zabezpieczeń sprzętu (HSM) fips 140-2 poziomu 2 podczas przechowywania danych konfiguracyjnych.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakiety

Dodaj odwołanie do pakietu [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Przykładowa aplikacja

Przykładowa aplikacja działa w jednym z dwóch `#define` trybów określonych przez instrukcję u góry *pliku Program.cs:*

* `Certificate`&ndash; Demonstruje użycie identyfikatora klienta usługi Azure Key Vault i certyfikatu X.509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w usłudze Azure Key Vault. Tę wersję próbki można uruchomić z dowolnej lokalizacji, wdrożony w usłudze Azure App Service lub dowolnego hosta zdolnego do obsługi aplikacji ASP.NET Core.
* `Managed`&ndash; Pokazuje, jak używać [tożsamości zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) do uwierzytelniania aplikacji w usłudze Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji. Podczas korzystania z tożsamości zarządzanych do uwierzytelniania identyfikator aplikacji usługi Azure AD i hasło (klucz tajny klienta) nie są wymagane. Wersja `Managed` próbki musi zostać wdrożona na platformie Azure. Postępuj zgodnie ze wskazówkami w [sekcji Użyj tożsamości zarządzanych dla zasobów platformy Azure.](#use-managed-identities-for-azure-resources)

Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji`#define`przy <xref:index#preprocessor-directives-in-sample-code>użyciu dyrektyw preprocesora ( ), zobacz .

## <a name="secret-storage-in-the-development-environment"></a>Tajne przechowywanie w środowisku programistycznym

Ustaw wpisy tajne lokalnie za pomocą [narzędzia Menedżer tajny](xref:security/app-secrets). Gdy przykładowa aplikacja działa na komputerze lokalnym w środowisku programistycznym, wpisy tajne są ładowane z lokalnego magazynu Programu Secret Manager.

Narzędzie Secret Manager `<UserSecretsId>` wymaga właściwości w pliku projektu aplikacji. Ustaw wartość właściwości`{GUID}`( ) na dowolny unikatowy identyfikator GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Wpisy tajne są tworzone jako pary nazwa-wartość. Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w [nazwach kluczy konfiguracji ASP.NET Core.](xref:fundamentals/configuration/index)

Tajny menedżer jest używany z powłoki polecenia otwartej dla `{SECRET NAME}` katalogu głównego `{SECRET VALUE}` [zawartości](xref:fundamentals/index#content-root)projektu , gdzie jest nazwa i jest wartością:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Wykonaj następujące polecenia w powłoce poleceń z [katalogu głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Gdy te wpisy tajne są przechowywane w usłudze Azure Key Vault `_dev` w [magazynie tajnym w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Usługi Azure Key Vault, sufiks jest zmieniany na `_prod`. Sufiks zawiera wizualną wskazówkę w danych wyjściowych aplikacji wskazującą źródło wartości konfiguracji.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Tajne magazynowanie w środowisku produkcyjnym z usługą Azure Key Vault

Instrukcje dostarczone przez [Przewodnik Szybki start: Ustaw i pobierz klucz tajny z usługi Azure Key Vault przy użyciu narzędzia Azure CLI](/azure/key-vault/quick-create-cli) tematu są podsumowane w tym miejscu do tworzenia usługi Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację. Zapoznaj się z tematem, aby uzyskać więcej informacji.

1. Otwórz powłokę usługi Azure Cloud przy użyciu jednej z następujących metod w [witrynie Azure portal:](https://portal.azure.com/)

   * Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Użyj ciągu wyszukiwania "Azure CLI" w polu tekstowym.
   * Otwórz powłokę chmury w przeglądarce za pomocą przycisku **Uruchom powłokę cloud** shell.
   * Wybierz przycisk **Powłoki chmury** w menu w prawym górnym rogu witryny Azure portal.

   Aby uzyskać więcej informacji, zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure/) i [omówienie usługi Azure Cloud Shell](/azure/cloud-shell/overview).

1. Jeśli nie jesteś jeszcze uwierzytelniony, zaloguj `az login` się za pomocą polecenia.

1. Utwórz grupę zasobów za `{RESOURCE GROUP NAME}` pomocą następującego polecenia, gdzie jest `{LOCATION}` nazwa grupy zasobów dla nowej grupy zasobów i jest regionem platformy Azure (centrum danych):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Utwórz magazyn kluczy w grupie zasobów `{KEY VAULT NAME}` za pomocą następującego polecenia, `{LOCATION}` gdzie jest nazwa nowego magazynu kluczy i jest regionem platformy Azure (centrum danych):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Tworzenie wpisów tajnych w magazynie kluczy jako pary nazwa-wartość.

   Nazwy tajne usługi Azure Key Vault są ograniczone do znaków alfanumeryczne i kresek. Wartości hierarchiczne (sekcje `--` konfiguracji) używają (dwóch kresek) jako separatora. Dwukropki, które są zwykle używane do rozgraniczenia sekcji z podklucza w [ASP.NET konfiguracji rdzenia,](xref:fundamentals/configuration/index)nie są dozwolone w nazwach tajnych magazynu kluczy. W związku z tym dwa kreski są używane i zamienione na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.

   Następujące wpisy tajne są do użycia z przykładową aplikacją. Wartości zawierają `_prod` sufiks, aby `_dev` odróżnić je od wartości sufiksu załadowany w środowisku deweloperskim z wpisów tajnych użytkownika. Zamień `{KEY VAULT NAME}` nazwę magazynu kluczy utworzonego w poprzednim kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Używanie identyfikatora aplikacji i certyfikatu X.509 dla aplikacji nieobjętych platformą Azure

Skonfiguruj usługę Azure AD, usługę Azure Key Vault i aplikację do używania identyfikatora aplikacji usługi Azure Active Directory i certyfikatu X.509 do uwierzytelniania w magazynie kluczy, **gdy aplikacja jest hostowana poza platformą Azure.** Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Chociaż przy użyciu identyfikatora aplikacji i certyfikatu X.509 jest obsługiwany dla aplikacji hostowanych na platformie Azure, zalecamy używanie [tożsamości zarządzanych dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure. Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji lub w środowisku programistycznym.

Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X.509, gdy `#define` instrukcja w górnej części pliku *Program.cs* jest ustawiona na `Certificate`.

1. Utwórz certyfikat archiwum PKCS#12 (*.pfx*). Opcje tworzenia certyfikatów obejmują [MakeCert w systemie Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).
1. Zainstaluj certyfikat w magazynie certyfikatów osobistych bieżącego użytkownika. Oznaczenie klucza jako możliwego do wyeksportowania jest opcjonalne. Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.
1. Wyeksportuj certyfikat archiwum PKCS#12 (*.pfx*) jako certyfikat zakodowany w języku der (*.cer*).
1. Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).
1. Przekaż certyfikat zakodowany w stanie DER *(.cer)* do usługi Azure AD:
   1. Wybierz aplikację w usłudze Azure AD.
   1. Przejdź do **wpisów certyfikaty & wpisy tajne**.
   1. Wybierz **pozycję Przekaż certyfikat,** aby przekazać certyfikat zawierający klucz publiczny. Akceptowany jest certyfikat *.cer*, *.pem*lub *.crt.*
1. W pliku *appsettings.json* aplikacji przechowuj nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu.
1. Przejdź do **magazynów kluczy** w witrynie Azure portal.
1. Wybierz magazyn kluczy utworzony w [magazynie tajnym w środowisku produkcyjnym z sekcją Usługi Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Wybierz pozycję **Zasady dostępu**.
1. Wybierz **pozycję Dodaj zasady dostępu**.
1. Otwórz **uprawnienia tajne** i podaj aplikacji uprawnienia **Pobierz** i **Lista.**
1. Wybierz **pozycję Wybierz głównego zobowiązanego** i wybierz zarejestrowaną aplikację według nazwy. Wybierz przycisk **Wybierz.**
1. Kliknij przycisk **OK**.
1. Wybierz pozycję **Zapisz**.
1. Wdrażanie aplikacji.

Przykładowa `Certificate` aplikacja uzyskuje swoje `IConfigurationRoot` wartości konfiguracyjne o takiej samej nazwie jak nazwa tajna:

* Wartości nieobyaralne: `SecretName` Wartość dla `config["SecretName"]`jest uzyskiwana za pomocą .
* Wartości hierarchiczne (sekcje): Użyj `:` notacji `GetSection` (dwukropek) lub metody rozszerzenia. Użyj jednej z tych metod, aby uzyskać wartość konfiguracji:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certyfikat X.509 jest zarządzany przez system operacyjny. Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> z wartościami dostarczonymi przez plik *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Przykładowe wartości:

* Nazwa magazynu kluczy:`contosovault`
* Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`
* Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne. W środowisku programistycznym tajne `_dev` wartości ładują się z sufiksem. W środowisku produkcyjnym wartości `_prod` ładują się z przyrostkiem.

## <a name="use-managed-identities-for-azure-resources"></a>Używanie tożsamości zarządzanych dla zasobów platformy Azure

**Aplikacja wdrożona na platformie Azure** może korzystać z [tożsamości zarządzanych dla zasobów platformy Azure,](/azure/active-directory/managed-identities-azure-resources/overview)co umożliwia aplikacji uwierzytelnianie za pomocą usługi Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń (identyfikator aplikacji i klucz tajny hasła/klienta) przechowywanych w aplikacji.

Przykładowa aplikacja używa tożsamości zarządzanych dla `#define` zasobów platformy Azure, gdy instrukcja u `Managed`góry pliku *Program.cs* jest ustawiona na .

Wprowadź nazwę przechowalni w pliku *appsettings.json* aplikacji. Przykładowa aplikacja nie wymaga identyfikatora aplikacji i hasła (klucz `Managed` tajny klienta) po ustawieniu wersji, dzięki czemu można zignorować te wpisy konfiguracji. Aplikacja jest wdrażana na platformie Azure, a platforma Azure uwierzytelnia aplikację, aby uzyskać dostęp do usługi Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w pliku *appsettings.json.*

Wdrażanie przykładowej aplikacji w usłudze Azure App Service.

Aplikacja wdrożona w usłudze Azure App Service jest automatycznie rejestrowana w usłudze Azure AD podczas tworzenia usługi. Uzyskaj identyfikator obiektu z wdrożenia do użycia w następującym poleceniu. Identyfikator obiektu jest wyświetlany w witrynie Azure portal w panelu **Tożsamość** usługi App Service.

Korzystając z interfejsu wiersza polecenia platformy Azure i `list` `get` identyfikatora obiektu aplikacji, podaj aplikacji i uprawnienia dostępu do magazynu kluczy:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Uruchom ponownie aplikację przy** użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub witryny Azure portal.

Przykładowa aplikacja:

* Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez ciągu połączenia. Gdy nie podano parametry połączenia, dostawca próbuje uzyskać token dostępu z tożsamości zarządzanych dla zasobów platformy Azure.
* Nowy <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest tworzony `AzureServiceTokenProvider` z wywołania zwrotnego tokenu wystąpienia.
* Wystąpienie <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest używane z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> domyślną implementacją, która ładuje wszystkie`--`wartości tajne i`:`zastępuje podwójne kreski ( ) dwukropkami ( ) w nazwach kluczy.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Przykładowa wartość nazwy magazynu kluczy:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne. W środowisku programistycznym `_dev` tajne wartości mają sufiks, ponieważ są one dostarczane przez wpisy tajne użytkownika. W środowisku produkcyjnym wartości `_prod` ładują się z sufiksem, ponieważ są one dostarczane przez usługę Azure Key Vault.

Jeśli zostanie `Access denied` wyświetlony błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i zapewnia dostęp do magazynu kluczy. Upewnij się, że usługa została ponownie uruchomiona na platformie Azure.

Aby uzyskać informacje na temat korzystania z dostawcy z tożsamością zarządzaną i potokiem usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Usługi Azure Resource Manager z maszyną wirtualną z tożsamością usługi zarządzanej.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="configuration-options"></a>Opcje konfiguracji

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>może <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>zaakceptować:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Właściwość         | Opis |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>do pobierania wartości. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instancji używanej do kontrolowania ładowania tajnego. |
| `ReloadInterval` | `Timespan`czekać między próbami sondowania magazynu kluczy dla zmian. Wartością domyślną jest `null` (konfiguracja nie jest ponownie załadowana). |
| `Vault`          | Identyfikator URI magazynu kluczy. |

## <a name="use-a-key-name-prefix"></a>Używanie prefiksu nazwy klucza

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia przeciążenie, które akceptuje <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementację programu , co pozwala kontrolować sposób konwersji wpisów tajnych magazynu kluczy na klucze konfiguracji. Na przykład można zaimplementować interfejs, aby załadować wartości tajne na podstawie wartości prefiksu, które są podane podczas uruchamiania aplikacji. Dzięki temu można na przykład załadować wpisy tajne na podstawie wersji aplikacji.

> [!WARNING]
> Nie używaj prefiksów w kluczu do wpisów tajnych magazynu kluczy, aby umieścić wpisy tajne dla wielu aplikacji w tym samym magazynie kluczy lub umieścić wpisy tajne środowiska (na przykład *wpisy* tajne rozwoju i *produkcji)* w tym samym magazynie. Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperów/produkcji używać oddzielnych magazynów kluczy do izolowania środowisk aplikacji dla najwyższego poziomu zabezpieczeń.

W poniższym przykładzie klucz tajny jest ustanawiany w magazynie kluczy (i `5000-AppSecret` przy użyciu narzędzia Menedżera tajnego dla środowiska programistycznego) dla (okresy nie są dozwolone w nazwach tajnych magazynu kluczy). Ten klucz tajny reprezentuje klucz tajny aplikacji dla wersji 5.0.0.0 aplikacji. W przypadku innej wersji aplikacji, 5.1.0.0, klucz tajny jest dodawany do magazynu `5100-AppSecret`kluczy (i za pomocą narzędzia Secret Manager) dla . Każda wersja aplikacji ładuje swoją wersją `AppSecret`tajną wartość do swojej konfiguracji jako , odpędzając wersję, ponieważ ładuje klucz tajny.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>nazywa się z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>niestandardowym:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Implementacja <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na prefiksy wersji wpisów tajnych, aby załadować odpowiedni klucz tajny do konfiguracji:

* `Load`ładuje klucz tajny, gdy jego nazwa zaczyna się od prefiksu. Inne wpisy tajne nie są ładowane.
* `GetKey`:
  * Usuwa prefiks z tajnej nazwy.
  * Zastępuje dwie kreski w dowolnej `KeyDelimiter`nazwie , który jest ogranicznikiem używanym w konfiguracji (zwykle dwukropek). Usługa Azure Key Vault nie zezwala na dwukropek w tajnych nazwach.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Metoda `Load` jest wywoływana przez algorytm dostawcy, który iteruje za pośrednictwem wpisów tajnych magazynu, aby znaleźć te, które mają prefiks wersji. Po znalezieniu prefiksu wersji z `Load` `GetKey` , algorytm używa metody, aby zwrócić nazwę konfiguracji nazwy tajnej. Usuwa prefiks wersji z nazwy klucza tajnego i zwraca pozostałą część nazwy tajnej do ładowania do par nazwa-wartość konfiguracji aplikacji.

Gdy takie podejście jest wdrażane:

1. Wersja aplikacji określona w pliku projektu aplikacji. W poniższym przykładzie wersja aplikacji jest `5.0.0.0`ustawiona na:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Upewnij się, że `<UserSecretsId>` właściwość jest obecna w `{GUID}` pliku projektu aplikacji, gdzie jest identyfikator GUID dostarczony przez użytkownika:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Zapisz następujące wpisy tajne lokalnie za pomocą [narzędzia Secret Manager:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Wpisy tajne są zapisywane w usłudze Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy. Klucz tajny `5000-AppSecret` ciągu jest dopasowywał się do wersji aplikacji określonej w pliku projektu aplikacji (`5.0.0.0`).

1. Wersja `5000` (z kreską) jest usuwana z nazwy klucza. W całej aplikacji odczyt konfiguracji `AppSecret` z kluczem ładuje wartość tajną.

1. Jeśli wersja aplikacji zostanie zmieniona w `5.1.0.0` pliku projektu i aplikacja jest uruchamiana `5.1.0.0_secret_value_dev` ponownie, wartość `5.1.0.0_secret_value_prod` klucza tajnego zwracana jest w środowisku deweloperskim i w środowisku produkcyjnym.

> [!NOTE]
> Można również podać <xref:Microsoft.Azure.KeyVault.KeyVaultClient> własną <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementację do . Klient niestandardowy zezwala na udostępnianie pojedynczego wystąpienia klienta w aplikacji.

## <a name="bind-an-array-to-a-class"></a>Powiąż tablicę z klasą

Dostawca jest w stanie odczytywać wartości konfiguracji do tablicy do powiązania z tablicą POCO.

Podczas odczytu ze źródła konfiguracji, które`:`umożliwia klucze zawierają separatory dwukropka ( ) segment`:0:` `:1:`klucza numerycznego służy do rozróżniania klawiszy tworzących tablicę ( , , &hellip; `:{n}:`). Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klucze usługi Azure Key Vault nie mogą używać dwukropka jako separatora. Podejście opisane w tym temacie używa podwójnych kresek (`--`) jako separatora dla wartości hierarchicznych (sekcje). Klucze tablic są przechowywane w usłudze Azure Key Vault`--0--` `--1--`z &hellip; `--{n}--`podwójnymi myślnikami i segmentami kluczy numerycznych ( , , ).

Sprawdź następującą konfigurację dostawcy rejestrowania [serilogu](https://serilog.net/) dostarczoną przez plik JSON. Istnieją dwa literały obiektu zdefiniowane w `WriteTo` tablicy, które odzwierciedlają dwa ujścia Serilog , które opisują miejsc docelowych dla rejestrowania danych *wyjściowych:*

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

Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana`--`w usłudze Azure Key Vault przy użyciu notacji podwójnej kreski ( ) i segmentów liczbowych:

| Klucz | Wartość |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Przeładuj sekrety

Wpisy tajne `IConfigurationRoot.Reload()` są buforowane, dopóki nie zostanie wywołana. Wygasłe, wyłączone i zaktualizowane wpisy tajne w magazynie `Reload` kluczy nie są przestrzegane przez aplikację, dopóki nie zostanie wykonana.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Wyłączone i wygasłe wpisy tajne

Wyłączone i wygasłe <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>wpisy tajne rzucać . Aby zapobiec zrzucaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj wyłączony lub wygasły klucz tajny.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywany w [ASP.NET infrastruktury rejestrowania rdzenia](xref:fundamentals/logging/index). Następujące warunki uniemożliwią ładowanie konfiguracji:

* Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w usłudze Azure Active Directory.
* Magazyn kluczy nie istnieje w usłudze Azure Key Vault.
* Aplikacja nie jest autoryzowana do uzyskiwania dostępu do magazynu kluczy.
* Zasady dostępu nie obejmują `Get` `List` i uprawnienia.
* W magazynie kluczy dane konfiguracji (para nazwa-wartość) są niepoprawnie nazwane, brakujące, wyłączone lub wygasłe.
* Aplikacja ma nieprawidłową nazwę`KeyVaultName`magazynu kluczy (`AzureADApplicationId`), identyfikator aplikacji usługi`AzureADCertThumbprint`Azure AD ( lub odcisk palca certyfikatu usługi Azure AD ( ).
* Klucz konfiguracji (nazwa) jest niepoprawna w aplikacji dla wartości, którą próbujesz załadować.
* Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy została utworzona zasada, ale przycisk **Zapisz** nie został wybrany w interfejsie użytkownika **zasad dostępu.**

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Przechowalnia kluczy](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: dokumentacja magazynu kluczy](/azure/key-vault/)
* [Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Klasa KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET](/azure/key-vault/quick-create-net)
* [Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym dokumencie wyjaśniono, jak używać dostawcy konfiguracji [usługi Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do ładowania wartości konfiguracji aplikacji z wpisów tajnych usługi Azure Key Vault. Usługa Azure Key Vault to usługa oparta na chmurze, która pomaga w ochronie kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje i usługi. Typowe scenariusze korzystania z usługi Azure Key Vault z aplikacjami ASP.NET Core obejmują:

* Kontrolowanie dostępu do poufnych danych konfiguracyjnych.
* Spełnienie wymagań dotyczących sprawdzonych modułów zabezpieczeń sprzętu (HSM) fips 140-2 poziomu 2 podczas przechowywania danych konfiguracyjnych.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakiety

Dodaj odwołanie do pakietu [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Przykładowa aplikacja

Przykładowa aplikacja działa w jednym z dwóch `#define` trybów określonych przez instrukcję u góry *pliku Program.cs:*

* `Certificate`&ndash; Demonstruje użycie identyfikatora klienta usługi Azure Key Vault i certyfikatu X.509 w celu uzyskania dostępu do wpisów tajnych przechowywanych w usłudze Azure Key Vault. Tę wersję próbki można uruchomić z dowolnej lokalizacji, wdrożony w usłudze Azure App Service lub dowolnego hosta zdolnego do obsługi aplikacji ASP.NET Core.
* `Managed`&ndash; Pokazuje, jak używać [tożsamości zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) do uwierzytelniania aplikacji w usłudze Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń przechowywanych w kodzie lub konfiguracji aplikacji. Podczas korzystania z tożsamości zarządzanych do uwierzytelniania identyfikator aplikacji usługi Azure AD i hasło (klucz tajny klienta) nie są wymagane. Wersja `Managed` próbki musi zostać wdrożona na platformie Azure. Postępuj zgodnie ze wskazówkami w [sekcji Użyj tożsamości zarządzanych dla zasobów platformy Azure.](#use-managed-identities-for-azure-resources)

Aby uzyskać więcej informacji na temat konfigurowania przykładowej aplikacji`#define`przy <xref:index#preprocessor-directives-in-sample-code>użyciu dyrektyw preprocesora ( ), zobacz .

## <a name="secret-storage-in-the-development-environment"></a>Tajne przechowywanie w środowisku programistycznym

Ustaw wpisy tajne lokalnie za pomocą [narzędzia Menedżer tajny](xref:security/app-secrets). Gdy przykładowa aplikacja działa na komputerze lokalnym w środowisku programistycznym, wpisy tajne są ładowane z lokalnego magazynu Programu Secret Manager.

Narzędzie Secret Manager `<UserSecretsId>` wymaga właściwości w pliku projektu aplikacji. Ustaw wartość właściwości`{GUID}`( ) na dowolny unikatowy identyfikator GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Wpisy tajne są tworzone jako pary nazwa-wartość. Wartości hierarchiczne (sekcje konfiguracji) używają `:` (dwukropek) jako separatora w [nazwach kluczy konfiguracji ASP.NET Core.](xref:fundamentals/configuration/index)

Tajny menedżer jest używany z powłoki polecenia otwartej dla `{SECRET NAME}` katalogu głównego `{SECRET VALUE}` [zawartości](xref:fundamentals/index#content-root)projektu , gdzie jest nazwa i jest wartością:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Wykonaj następujące polecenia w powłoce poleceń z [katalogu głównego zawartości](xref:fundamentals/index#content-root) projektu, aby ustawić wpisy tajne dla przykładowej aplikacji:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Gdy te wpisy tajne są przechowywane w usłudze Azure Key Vault `_dev` w [magazynie tajnym w środowisku produkcyjnym z](#secret-storage-in-the-production-environment-with-azure-key-vault) sekcją Usługi Azure Key Vault, sufiks jest zmieniany na `_prod`. Sufiks zawiera wizualną wskazówkę w danych wyjściowych aplikacji wskazującą źródło wartości konfiguracji.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Tajne magazynowanie w środowisku produkcyjnym z usługą Azure Key Vault

Instrukcje dostarczone przez [Przewodnik Szybki start: Ustaw i pobierz klucz tajny z usługi Azure Key Vault przy użyciu narzędzia Azure CLI](/azure/key-vault/quick-create-cli) tematu są podsumowane w tym miejscu do tworzenia usługi Azure Key Vault i przechowywania wpisów tajnych używanych przez przykładową aplikację. Zapoznaj się z tematem, aby uzyskać więcej informacji.

1. Otwórz powłokę usługi Azure Cloud przy użyciu jednej z następujących metod w [witrynie Azure portal:](https://portal.azure.com/)

   * Wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Użyj ciągu wyszukiwania "Azure CLI" w polu tekstowym.
   * Otwórz powłokę chmury w przeglądarce za pomocą przycisku **Uruchom powłokę cloud** shell.
   * Wybierz przycisk **Powłoki chmury** w menu w prawym górnym rogu witryny Azure portal.

   Aby uzyskać więcej informacji, zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure/) i [omówienie usługi Azure Cloud Shell](/azure/cloud-shell/overview).

1. Jeśli nie jesteś jeszcze uwierzytelniony, zaloguj `az login` się za pomocą polecenia.

1. Utwórz grupę zasobów za `{RESOURCE GROUP NAME}` pomocą następującego polecenia, gdzie jest `{LOCATION}` nazwa grupy zasobów dla nowej grupy zasobów i jest regionem platformy Azure (centrum danych):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Utwórz magazyn kluczy w grupie zasobów `{KEY VAULT NAME}` za pomocą następującego polecenia, `{LOCATION}` gdzie jest nazwa nowego magazynu kluczy i jest regionem platformy Azure (centrum danych):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Tworzenie wpisów tajnych w magazynie kluczy jako pary nazwa-wartość.

   Nazwy tajne usługi Azure Key Vault są ograniczone do znaków alfanumeryczne i kresek. Wartości hierarchiczne (sekcje `--` konfiguracji) używają (dwóch kresek) jako separatora. Dwukropki, które są zwykle używane do rozgraniczenia sekcji z podklucza w [ASP.NET konfiguracji rdzenia,](xref:fundamentals/configuration/index)nie są dozwolone w nazwach tajnych magazynu kluczy. W związku z tym dwa kreski są używane i zamienione na dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.

   Następujące wpisy tajne są do użycia z przykładową aplikacją. Wartości zawierają `_prod` sufiks, aby `_dev` odróżnić je od wartości sufiksu załadowany w środowisku deweloperskim z wpisów tajnych użytkownika. Zamień `{KEY VAULT NAME}` nazwę magazynu kluczy utworzonego w poprzednim kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Używanie identyfikatora aplikacji i certyfikatu X.509 dla aplikacji nieobjętych platformą Azure

Skonfiguruj usługę Azure AD, usługę Azure Key Vault i aplikację do używania identyfikatora aplikacji usługi Azure Active Directory i certyfikatu X.509 do uwierzytelniania w magazynie kluczy, **gdy aplikacja jest hostowana poza platformą Azure.** Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Chociaż przy użyciu identyfikatora aplikacji i certyfikatu X.509 jest obsługiwany dla aplikacji hostowanych na platformie Azure, zalecamy używanie [tożsamości zarządzanych dla zasobów platformy Azure](#use-managed-identities-for-azure-resources) podczas hostowania aplikacji na platformie Azure. Tożsamości zarządzane nie wymagają przechowywania certyfikatu w aplikacji lub w środowisku programistycznym.

Przykładowa aplikacja używa identyfikatora aplikacji i certyfikatu X.509, gdy `#define` instrukcja w górnej części pliku *Program.cs* jest ustawiona na `Certificate`.

1. Utwórz certyfikat archiwum PKCS#12 (*.pfx*). Opcje tworzenia certyfikatów obejmują [MakeCert w systemie Windows](/windows/desktop/seccrypto/makecert) i [OpenSSL](https://www.openssl.org/).
1. Zainstaluj certyfikat w magazynie certyfikatów osobistych bieżącego użytkownika. Oznaczenie klucza jako możliwego do wyeksportowania jest opcjonalne. Zanotuj odcisk palca certyfikatu, który jest używany w dalszej części tego procesu.
1. Wyeksportuj certyfikat archiwum PKCS#12 (*.pfx*) jako certyfikat zakodowany w języku der (*.cer*).
1. Zarejestruj aplikację w usłudze Azure AD (**rejestracje aplikacji**).
1. Przekaż certyfikat zakodowany w stanie DER *(.cer)* do usługi Azure AD:
   1. Wybierz aplikację w usłudze Azure AD.
   1. Przejdź do **wpisów certyfikaty & wpisy tajne**.
   1. Wybierz **pozycję Przekaż certyfikat,** aby przekazać certyfikat zawierający klucz publiczny. Akceptowany jest certyfikat *.cer*, *.pem*lub *.crt.*
1. W pliku *appsettings.json* aplikacji przechowuj nazwę magazynu kluczy, identyfikator aplikacji i odcisk palca certyfikatu.
1. Przejdź do **magazynów kluczy** w witrynie Azure portal.
1. Wybierz magazyn kluczy utworzony w [magazynie tajnym w środowisku produkcyjnym z sekcją Usługi Azure Key Vault.](#secret-storage-in-the-production-environment-with-azure-key-vault)
1. Wybierz pozycję **Zasady dostępu**.
1. Wybierz **pozycję Dodaj zasady dostępu**.
1. Otwórz **uprawnienia tajne** i podaj aplikacji uprawnienia **Pobierz** i **Lista.**
1. Wybierz **pozycję Wybierz głównego zobowiązanego** i wybierz zarejestrowaną aplikację według nazwy. Wybierz przycisk **Wybierz.**
1. Kliknij przycisk **OK**.
1. Wybierz pozycję **Zapisz**.
1. Wdrażanie aplikacji.

Przykładowa `Certificate` aplikacja uzyskuje swoje `IConfigurationRoot` wartości konfiguracyjne o takiej samej nazwie jak nazwa tajna:

* Wartości nieobyaralne: `SecretName` Wartość dla `config["SecretName"]`jest uzyskiwana za pomocą .
* Wartości hierarchiczne (sekcje): Użyj `:` notacji `GetSection` (dwukropek) lub metody rozszerzenia. Użyj jednej z tych metod, aby uzyskać wartość konfiguracji:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certyfikat X.509 jest zarządzany przez system operacyjny. Aplikacja wywołuje <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> z wartościami dostarczonymi przez plik *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Przykładowe wartości:

* Nazwa magazynu kluczy:`contosovault`
* Identyfikator aplikacji:`627e911e-43cc-61d4-992e-12db9c81b413`
* Odcisk palca certyfikatu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne. W środowisku programistycznym tajne `_dev` wartości ładują się z sufiksem. W środowisku produkcyjnym wartości `_prod` ładują się z przyrostkiem.

## <a name="use-managed-identities-for-azure-resources"></a>Używanie tożsamości zarządzanych dla zasobów platformy Azure

**Aplikacja wdrożona na platformie Azure** może korzystać z [tożsamości zarządzanych dla zasobów platformy Azure,](/azure/active-directory/managed-identities-azure-resources/overview)co umożliwia aplikacji uwierzytelnianie za pomocą usługi Azure Key Vault przy użyciu uwierzytelniania usługi Azure AD bez poświadczeń (identyfikator aplikacji i klucz tajny hasła/klienta) przechowywanych w aplikacji.

Przykładowa aplikacja używa tożsamości zarządzanych dla `#define` zasobów platformy Azure, gdy instrukcja u `Managed`góry pliku *Program.cs* jest ustawiona na .

Wprowadź nazwę przechowalni w pliku *appsettings.json* aplikacji. Przykładowa aplikacja nie wymaga identyfikatora aplikacji i hasła (klucz `Managed` tajny klienta) po ustawieniu wersji, dzięki czemu można zignorować te wpisy konfiguracji. Aplikacja jest wdrażana na platformie Azure, a platforma Azure uwierzytelnia aplikację, aby uzyskać dostęp do usługi Azure Key Vault tylko przy użyciu nazwy magazynu przechowywanej w pliku *appsettings.json.*

Wdrażanie przykładowej aplikacji w usłudze Azure App Service.

Aplikacja wdrożona w usłudze Azure App Service jest automatycznie rejestrowana w usłudze Azure AD podczas tworzenia usługi. Uzyskaj identyfikator obiektu z wdrożenia do użycia w następującym poleceniu. Identyfikator obiektu jest wyświetlany w witrynie Azure portal w panelu **Tożsamość** usługi App Service.

Korzystając z interfejsu wiersza polecenia platformy Azure i `list` `get` identyfikatora obiektu aplikacji, podaj aplikacji i uprawnienia dostępu do magazynu kluczy:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Uruchom ponownie aplikację przy** użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub witryny Azure portal.

Przykładowa aplikacja:

* Tworzy wystąpienie `AzureServiceTokenProvider` klasy bez ciągu połączenia. Gdy nie podano parametry połączenia, dostawca próbuje uzyskać token dostępu z tożsamości zarządzanych dla zasobów platformy Azure.
* Nowy <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest tworzony `AzureServiceTokenProvider` z wywołania zwrotnego tokenu wystąpienia.
* Wystąpienie <xref:Microsoft.Azure.KeyVault.KeyVaultClient> jest używane z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> domyślną implementacją, która ładuje wszystkie`--`wartości tajne i`:`zastępuje podwójne kreski ( ) dwukropkami ( ) w nazwach kluczy.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Przykładowa wartość nazwy magazynu kluczy:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Po uruchomieniu aplikacji na stronie sieci Web są wyświetlane załadowane wartości tajne. W środowisku programistycznym `_dev` tajne wartości mają sufiks, ponieważ są one dostarczane przez wpisy tajne użytkownika. W środowisku produkcyjnym wartości `_prod` ładują się z sufiksem, ponieważ są one dostarczane przez usługę Azure Key Vault.

Jeśli zostanie `Access denied` wyświetlony błąd, upewnij się, że aplikacja jest zarejestrowana w usłudze Azure AD i zapewnia dostęp do magazynu kluczy. Upewnij się, że usługa została ponownie uruchomiona na platformie Azure.

Aby uzyskać informacje na temat korzystania z dostawcy z tożsamością zarządzaną i potokiem usługi Azure DevOps, zobacz [Tworzenie połączenia usługi Usługi Azure Resource Manager z maszyną wirtualną z tożsamością usługi zarządzanej.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="use-a-key-name-prefix"></a>Używanie prefiksu nazwy klucza

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>zapewnia przeciążenie, które akceptuje <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementację programu , co pozwala kontrolować sposób konwersji wpisów tajnych magazynu kluczy na klucze konfiguracji. Na przykład można zaimplementować interfejs, aby załadować wartości tajne na podstawie wartości prefiksu, które są podane podczas uruchamiania aplikacji. Dzięki temu można na przykład załadować wpisy tajne na podstawie wersji aplikacji.

> [!WARNING]
> Nie używaj prefiksów w kluczu do wpisów tajnych magazynu kluczy, aby umieścić wpisy tajne dla wielu aplikacji w tym samym magazynie kluczy lub umieścić wpisy tajne środowiska (na przykład *wpisy* tajne rozwoju i *produkcji)* w tym samym magazynie. Firma Microsoft zaleca, aby różne aplikacje i środowiska deweloperów/produkcji używać oddzielnych magazynów kluczy do izolowania środowisk aplikacji dla najwyższego poziomu zabezpieczeń.

W poniższym przykładzie klucz tajny jest ustanawiany w magazynie kluczy (i `5000-AppSecret` przy użyciu narzędzia Menedżera tajnego dla środowiska programistycznego) dla (okresy nie są dozwolone w nazwach tajnych magazynu kluczy). Ten klucz tajny reprezentuje klucz tajny aplikacji dla wersji 5.0.0.0 aplikacji. W przypadku innej wersji aplikacji, 5.1.0.0, klucz tajny jest dodawany do magazynu `5100-AppSecret`kluczy (i za pomocą narzędzia Secret Manager) dla . Każda wersja aplikacji ładuje swoją wersją `AppSecret`tajną wartość do swojej konfiguracji jako , odpędzając wersję, ponieważ ładuje klucz tajny.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>nazywa się z <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>niestandardowym:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Implementacja <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na prefiksy wersji wpisów tajnych, aby załadować odpowiedni klucz tajny do konfiguracji:

* `Load`ładuje klucz tajny, gdy jego nazwa zaczyna się od prefiksu. Inne wpisy tajne nie są ładowane.
* `GetKey`:
  * Usuwa prefiks z tajnej nazwy.
  * Zastępuje dwie kreski w dowolnej `KeyDelimiter`nazwie , który jest ogranicznikiem używanym w konfiguracji (zwykle dwukropek). Usługa Azure Key Vault nie zezwala na dwukropek w tajnych nazwach.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Metoda `Load` jest wywoływana przez algorytm dostawcy, który iteruje za pośrednictwem wpisów tajnych magazynu, aby znaleźć te, które mają prefiks wersji. Po znalezieniu prefiksu wersji z `Load` `GetKey` , algorytm używa metody, aby zwrócić nazwę konfiguracji nazwy tajnej. Usuwa prefiks wersji z nazwy klucza tajnego i zwraca pozostałą część nazwy tajnej do ładowania do par nazwa-wartość konfiguracji aplikacji.

Gdy takie podejście jest wdrażane:

1. Wersja aplikacji określona w pliku projektu aplikacji. W poniższym przykładzie wersja aplikacji jest `5.0.0.0`ustawiona na:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Upewnij się, że `<UserSecretsId>` właściwość jest obecna w `{GUID}` pliku projektu aplikacji, gdzie jest identyfikator GUID dostarczony przez użytkownika:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Zapisz następujące wpisy tajne lokalnie za pomocą [narzędzia Secret Manager:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Wpisy tajne są zapisywane w usłudze Azure Key Vault przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Po uruchomieniu aplikacji są ładowane wpisy tajne magazynu kluczy. Klucz tajny `5000-AppSecret` ciągu jest dopasowywał się do wersji aplikacji określonej w pliku projektu aplikacji (`5.0.0.0`).

1. Wersja `5000` (z kreską) jest usuwana z nazwy klucza. W całej aplikacji odczyt konfiguracji `AppSecret` z kluczem ładuje wartość tajną.

1. Jeśli wersja aplikacji zostanie zmieniona w `5.1.0.0` pliku projektu i aplikacja jest uruchamiana `5.1.0.0_secret_value_dev` ponownie, wartość `5.1.0.0_secret_value_prod` klucza tajnego zwracana jest w środowisku deweloperskim i w środowisku produkcyjnym.

> [!NOTE]
> Można również podać <xref:Microsoft.Azure.KeyVault.KeyVaultClient> własną <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementację do . Klient niestandardowy zezwala na udostępnianie pojedynczego wystąpienia klienta w aplikacji.

## <a name="bind-an-array-to-a-class"></a>Powiąż tablicę z klasą

Dostawca jest w stanie odczytywać wartości konfiguracji do tablicy do powiązania z tablicą POCO.

Podczas odczytu ze źródła konfiguracji, które`:`umożliwia klucze zawierają separatory dwukropka ( ) segment`:0:` `:1:`klucza numerycznego służy do rozróżniania klawiszy tworzących tablicę ( , , &hellip; `:{n}:`). Aby uzyskać więcej informacji, zobacz [Konfiguracja: Powiąż tablicę z klasą](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klucze usługi Azure Key Vault nie mogą używać dwukropka jako separatora. Podejście opisane w tym temacie używa podwójnych kresek (`--`) jako separatora dla wartości hierarchicznych (sekcje). Klucze tablic są przechowywane w usłudze Azure Key Vault`--0--` `--1--`z &hellip; `--{n}--`podwójnymi myślnikami i segmentami kluczy numerycznych ( , , ).

Sprawdź następującą konfigurację dostawcy rejestrowania [serilogu](https://serilog.net/) dostarczoną przez plik JSON. Istnieją dwa literały obiektu zdefiniowane w `WriteTo` tablicy, które odzwierciedlają dwa ujścia Serilog , które opisują miejsc docelowych dla rejestrowania danych *wyjściowych:*

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

Konfiguracja pokazana w poprzednim pliku JSON jest przechowywana`--`w usłudze Azure Key Vault przy użyciu notacji podwójnej kreski ( ) i segmentów liczbowych:

| Klucz | Wartość |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Przeładuj sekrety

Wpisy tajne `IConfigurationRoot.Reload()` są buforowane, dopóki nie zostanie wywołana. Wygasłe, wyłączone i zaktualizowane wpisy tajne w magazynie `Reload` kluczy nie są przestrzegane przez aplikację, dopóki nie zostanie wykonana.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Wyłączone i wygasłe wpisy tajne

Wyłączone i wygasłe <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>wpisy tajne rzucać . Aby zapobiec zrzucaniu aplikacji, podaj konfigurację przy użyciu innego dostawcy konfiguracji lub zaktualizuj wyłączony lub wygasły klucz tajny.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Gdy aplikacja nie może załadować konfiguracji przy użyciu dostawcy, komunikat o błędzie jest zapisywany w [ASP.NET infrastruktury rejestrowania rdzenia](xref:fundamentals/logging/index). Następujące warunki uniemożliwią ładowanie konfiguracji:

* Aplikacja lub certyfikat nie jest poprawnie skonfigurowany w usłudze Azure Active Directory.
* Magazyn kluczy nie istnieje w usłudze Azure Key Vault.
* Aplikacja nie jest autoryzowana do uzyskiwania dostępu do magazynu kluczy.
* Zasady dostępu nie obejmują `Get` `List` i uprawnienia.
* W magazynie kluczy dane konfiguracji (para nazwa-wartość) są niepoprawnie nazwane, brakujące, wyłączone lub wygasłe.
* Aplikacja ma nieprawidłową nazwę`KeyVaultName`magazynu kluczy (`AzureADApplicationId`), identyfikator aplikacji usługi`AzureADCertThumbprint`Azure AD ( lub odcisk palca certyfikatu usługi Azure AD ( ).
* Klucz konfiguracji (nazwa) jest niepoprawna w aplikacji dla wartości, którą próbujesz załadować.
* Podczas dodawania zasad dostępu dla aplikacji do magazynu kluczy została utworzona zasada, ale przycisk **Zapisz** nie został wybrany w interfejsie użytkownika **zasad dostępu.**

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Przechowalnia kluczy](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: dokumentacja magazynu kluczy](/azure/key-vault/)
* [Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Klasa KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu aplikacji internetowej .NET](/azure/key-vault/quick-create-net)
* [Samouczek: jak używać usługi Azure Key Vault za pomocą maszyny wirtualnej platformy Azure z systemem Windows na platformie .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

