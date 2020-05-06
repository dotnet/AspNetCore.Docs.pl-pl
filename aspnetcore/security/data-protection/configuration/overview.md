---
title: Konfigurowanie ochrony danych ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować ochronę danych w programie ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/overview
ms.openlocfilehash: d37fdc44bb9a603d85818fc72a7a07de67006366
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776815"
---
# <a name="configure-aspnet-core-data-protection"></a>Konfigurowanie ochrony danych ASP.NET Core

Gdy system ochrony danych jest zainicjowany, stosuje [Ustawienia domyślne](xref:security/data-protection/configuration/default-settings) w oparciu o środowisko operacyjne. Te ustawienia są zwykle odpowiednie dla aplikacji uruchomionych na pojedynczym komputerze. Istnieją przypadki, w których deweloper może chcieć zmienić ustawienia domyślne:

* Aplikacja jest rozłożona na wiele maszyn.
* Ze względów zgodności.

W tych scenariuszach system ochrony danych oferuje bogaty interfejs API konfiguracji.

> [!WARNING]
> Podobnie jak w przypadku plików konfiguracji pierścień klucza ochrony danych powinien być chroniony przy użyciu odpowiednich uprawnień. Możesz zaszyfrować klucze w spoczynku, ale nie uniemożliwi to osobom atakującym tworzenie nowych kluczy. W związku z tym ma to wpływ na zabezpieczenia aplikacji. Dostęp do lokalizacji magazynu skonfigurowanej przy użyciu ochrony danych powinien być ograniczony do samej aplikacji, podobnie jak w przypadku ochrony plików konfiguracyjnych. Na przykład jeśli zdecydujesz się na przechowywanie magazynu kluczy na dysku, użyj uprawnień systemu plików. Upewnij się, że tylko tożsamość, w ramach której działa aplikacja sieci Web, ma uprawnienia do odczytu, zapisu i tworzenia dostępu do tego katalogu. W przypadku korzystania z usługi Azure Blob Storage tylko aplikacja sieci Web powinna mieć możliwość odczytywania, zapisywania lub tworzenia nowych wpisów w magazynie obiektów BLOB itd.
>
> Metoda rozszerzająca [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) zwraca [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`Opisuje metody rozszerzające, które można połączyć ze sobą w celu skonfigurowania opcji ochrony danych.

::: moniker range=">= aspnetcore-3.0"

W przypadku rozszerzeń ochrony danych używanych w tym artykule wymagane są następujące pakiety NuGet:

* [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [Microsoft. AspNetCore. dataprotection. AzureKeyVault](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Aby przechowywać klucze w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), skonfiguruj system przy użyciu [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) w `Startup` klasie:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Ustaw lokalizację magazynu kluczy (na przykład [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Należy ustawić lokalizację, ponieważ wywołuje `ProtectKeysWithAzureKeyVault` implementację [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , która wyłącza ustawienia automatycznego ochrony danych, w tym lokalizację magazynu kluczy. W powyższym przykładzie używa się usługi Azure Blob Storage, aby zachować pierścień kluczy. Aby uzyskać więcej informacji, zobacz [dostawcy magazynu kluczy: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Możesz również utrwalać pierścień kluczy lokalnie za pomocą [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier` Jest to identyfikator klucza magazynu kluczy używany do szyfrowania klucza. Na przykład klucz utworzony w magazynie kluczy o nazwie `dataprotection` w `contosokeyvault` ma identyfikator `https://contosokeyvault.vault.azure.net/keys/dataprotection/`klucza. Podaj aplikację z **odwinięciem klucza** i **zawiń uprawnienia klucza** do magazynu kluczy.

`ProtectKeysWithAzureKeyVault`przeciążenia

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) zezwala na użycie [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) , aby umożliwić systemowi ochrony danych korzystanie z magazynu kluczy.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) zezwala na użycie a `ClientId` i [certyfikatu x509](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) , aby umożliwić systemowi ochrony danych korzystanie z magazynu kluczy.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) umożliwia korzystanie z programu `ClientId` i `ClientSecret` , aby umożliwić systemowi ochrony danych korzystanie z magazynu kluczy.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Aby przechowywać klucze w udziale UNC zamiast w domyślnej lokalizacji *% LocalAppData%* , skonfiguruj system przy użyciu [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Jeśli zmienisz lokalizację trwałości klucza, system przestanie automatycznie szyfrować klucze w spoczynku, ponieważ nie wie, czy DPAPI jest odpowiednim mechanizmem szyfrowania.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

System można skonfigurować tak, aby chronił klucze w spoczynku przez wywołanie dowolnego interfejsu API [konfiguracji\* ProtectKeysWith](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Rozważmy poniższy przykład, który przechowuje klucze w udziale UNC i szyfruje te klucze przy użyciu określonego certyfikatu X. 509:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

W ASP.NET Core 2,1 lub nowszej można podać [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) do [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), takie jak certyfikat załadowany z pliku:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

Zapoznaj się z tematem [szyfrowanie kluczy w spoczynku](xref:security/data-protection/implementation/key-encryption-at-rest) , aby uzyskać więcej przykładów i dyskusji na temat wbudowanych mechanizmów szyfrowania kluczy.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

W ASP.NET Core 2,1 lub nowszych można obrócić certyfikaty i odszyfrować klucze w spoczynku przy użyciu tablicy [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) Certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Aby skonfigurować system do używania okresu istnienia klucza wynoszącego 14 dni zamiast domyślnego 90 dni, należy użyć [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>Setapplicationname

Domyślnie system ochrony danych izoluje aplikacje od siebie w oparciu o ich ścieżki [Główne zawartości](xref:fundamentals/index#content-root) , nawet jeśli korzystają one z tego samego repozytorium klucza fizycznego. Zapobiega to zrozumieniu przez aplikacje innych chronionych ładunków.

Aby udostępnić chronione ładunki między aplikacjami:

* Skonfiguruj <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> w każdej aplikacji o tej samej wartości.
* Użyj tej samej wersji stosu interfejsu API ochrony danych w aplikacjach. Wykonaj **jedną** z następujących czynności w plikach projektu aplikacji:
  * Odwołuje się do tej samej udostępnionej wersji platformy za pośrednictwem [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Odwołuje się do tej samej wersji [pakietu ochrony danych](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Może wystąpić scenariusz, w którym aplikacja nie powinna automatycznie rzutować kluczy (utworzyć nowe klucze), ponieważ zbliżają się one do wygaśnięcia. Przykładem mogą być aplikacje skonfigurowane w relacji głównej/pomocniczej, w której tylko podstawowa aplikacja jest odpowiedzialna za problemy związane z zarządzaniem kluczami, a aplikacje pomocnicze po prostu mają widok tylko do odczytu dzwonka klucza. Aplikacje pomocnicze można skonfigurować do traktowania pierścienia kluczy jako tylko do odczytu przez skonfigurowanie systemu przy użyciu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Izolacja na poziomie aplikacji

Gdy system ochrony danych jest dostarczany przez ASP.NET Core hosta, automatycznie izoluje aplikacje, nawet jeśli te aplikacje działają w ramach tego samego konta procesu roboczego i korzystają z tego samego głównego materiału klucza. Jest to nieco podobne do modyfikatora IsolateApps z `<machineKey>` elementu System. Web.

Mechanizm izolacji działa, biorąc pod uwagę każdą aplikację na komputerze lokalnym jako unikatową dzierżawę, a <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> tym samym w przypadku każdej z tych aplikacji automatycznie dołączenie identyfikatora aplikacji jako rozróżniacza. Unikatowy identyfikator aplikacji jest ścieżką fizyczną aplikacji:

* W przypadku aplikacji hostowanych w usługach IIS unikatowym IDENTYFIKATORem jest ścieżka fizyczna programu IIS aplikacji. Jeśli aplikacja jest wdrażana w środowisku kolektywu serwerów sieci Web, ta wartość jest stabilna przy założeniu, że środowiska IIS są skonfigurowane w podobny sposób na wszystkich komputerach w kolektywie serwerów sieci Web.
* W przypadku aplikacji, które są uruchomione na [serwerze Kestrel](xref:fundamentals/servers/index#kestrel), UNIKATOWYm identyfikatorem jest ścieżka fizyczna do aplikacji na dysku.

Unikatowy identyfikator został zaprojektowany w celu przerobienia&mdash;resetuje zarówno poszczególne aplikacje, jak i maszyny.

Ten mechanizm izolacji zakłada, że aplikacje nie są złośliwe. Złośliwa aplikacja zawsze ma wpływ na każdą inną aplikację uruchomioną w ramach tego samego konta procesu roboczego. W udostępnionym środowisku hostingu w przypadku, gdy aplikacje są wzajemnie niezaufane, dostawca hostingu powinien wykonać kroki, aby zapewnić izolację na poziomie systemu operacyjnego między aplikacjami, w tym oddzielić repozytoria klucza podstawowego aplikacji.

Jeśli system ochrony danych nie jest dostarczany przez hosta ASP.NET Core (na przykład w przypadku wystąpienia go za pośrednictwem `DataProtectionProvider` konkretnego typu) izolacja aplikacji jest domyślnie wyłączona. Gdy izolacja aplikacji jest wyłączona, wszystkie aplikacje obsługiwane przez ten sam materiał klucza mogą współużytkować ładunki, o ile będą one miały odpowiednie [cele](xref:security/data-protection/consumer-apis/purpose-strings). Aby zapewnić izolację aplikacji w tym środowisku, wywołaj metodę [Setapplicationname](#setapplicationname) w obiekcie Configuration i podaj unikatową nazwę dla każdej aplikacji.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Zmienianie algorytmów za pomocą UseCryptographicAlgorithms

Stos ochrony danych pozwala zmienić domyślny algorytm używany przez nowo generowane klucze. Najprostszym sposobem wykonania tej czynności jest wywołanie [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) z wywołania zwrotnego konfiguracji:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

Domyślnym EncryptionAlgorithm jest AES-256-CBC, a domyślnym ValidationAlgorithm jest HMACSHA256. Zasady domyślne mogą być ustawiane przez administratora systemu za pośrednictwem [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy), ale jawne wywołanie `UseCryptographicAlgorithms` przesłania zasady domyślne.

Wywołanie `UseCryptographicAlgorithms` pozwala określić żądany algorytm ze wstępnie zdefiniowanej wbudowanej listy. Nie musisz martwić się o implementację algorytmu. W powyższym scenariuszu system ochrony danych próbuje użyć implementacji CNG w przypadku uruchamiania w systemie Windows. W przeciwnym razie powraca do zarządzanej klasy [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .

Można ręcznie określić implementację za pośrednictwem wywołania [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Zmiana algorytmów nie ma wpływu na istniejące klucze w pęku kluczy. Ma to wpływ tylko na nowo wygenerowane klucze.

### <a name="specifying-custom-managed-algorithms"></a>Określanie niestandardowych algorytmów zarządzanych

::: moniker range=">= aspnetcore-2.0"

Aby określić niestandardowe algorytmy zarządzane, Utwórz wystąpienie [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) , które wskazuje na typy implementacji:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aby określić niestandardowe algorytmy zarządzane, Utwórz wystąpienie [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) , które wskazuje na typy implementacji:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

Ogólnie rzecz \*biorąc, właściwości typu muszą wskazywać na konkretny, instantiable (za pośrednictwem publicznego elementu ctor bez parametrów) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) i [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), chociaż system specjalne uwzględnia pewne wartości `typeof(Aes)` , takie jak dla wygody.

> [!NOTE]
> SymmetricAlgorithm musi mieć długość klucza wynoszącą ≥ 128 bitów i rozmiar bloku wynoszący ≥ 64 bitów i musi obsługiwać szyfrowanie w trybie CBC z dopełnieniem #7 PKCS. KeyedHashAlgorithm musi mieć rozmiar podsumowania >= 128 bitów i musi obsługiwać klucze długości równe długości skrótu algorytmu wyznaczania wartości skrótu. KeyedHashAlgorithm nie jest ściśle wymagana jako HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Określanie niestandardowych algorytmów CNG systemu Windows

::: moniker range=">= aspnetcore-2.0"

Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania w trybie CBC z walidacją HMAC, Utwórz wystąpienie [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) zawierające informacje o algorytmach:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania w trybie CBC z walidacją HMAC, Utwórz wystąpienie [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) zawierające informacje o algorytmach:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> Algorytm szyfrowania bloku symetrycznego musi mieć długość klucza wynoszącą >= 128 bitów, rozmiar bloku >= 64 bitów i musi obsługiwać szyfrowanie w trybie CBC z dopełnieniem #7 PKCS. Algorytm wyznaczania wartości skrótu musi mieć rozmiar Digest >= 128 bitów i musi być obsługiwany przy użyciu flagi\_flagi\_alg\_uchwytu HMAC\_(BCRYPT). Właściwości \*dostawcy można ustawić na wartość null, aby użyć domyślnego dostawcy dla określonego algorytmu. Aby uzyskać więcej informacji, zobacz dokumentację [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .

::: moniker range=">= aspnetcore-2.0"

Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania Galois/counter z walidacją, Utwórz wystąpienie [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) zawierające informacje o algorytmach:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania Galois/counter z walidacją, Utwórz wystąpienie [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) zawierające informacje o algorytmach:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> Algorytm szyfrowania bloku symetrycznego musi mieć długość klucza wynoszącą >= 128 bitów, rozmiar bloku równy 128 bitów i musi obsługiwać szyfrowanie GCM. Właściwość [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) można ustawić na wartość null, aby użyć domyślnego dostawcy dla określonego algorytmu. Aby uzyskać więcej informacji, zobacz dokumentację [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .

### <a name="specifying-other-custom-algorithms"></a>Określanie innych algorytmów niestandardowych

Chociaż nie jest udostępniany jako interfejs API pierwszej klasy, system ochrony danych jest wystarczająco rozszerzalny, aby można było określić niemal dowolny rodzaj algorytmu. Na przykład można zachować wszystkie klucze zawarte w sprzętowym module zabezpieczeń (HSM) i zapewnić niestandardową implementację podstawowych procedur szyfrowania i odszyfrowywania. Aby uzyskać więcej informacji, zobacz [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) z [rozszerzalnością kryptografii Core](xref:security/data-protection/extensibility/core-crypto) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Utrwalanie kluczy podczas hostowania w kontenerze platformy Docker

W przypadku hostowania w kontenerze [platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) klucze powinny być utrzymywane w obu:

* Folder, który jest woluminem platformy Docker, który utrzymuje się poza okresem istnienia kontenera, takim jak udostępniony wolumin lub wolumin zainstalowany na hoście.
* Dostawca zewnętrzny, taki jak [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) lub [Redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Utrwalanie kluczy za pomocą Redis

Tylko wersje Redis obsługujące [trwałość danych Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) powinny być używane do przechowywania kluczy. [Magazyn obiektów blob platformy Azure](/azure/storage/blobs/storage-blobs-introduction) jest trwały i może służyć do przechowywania kluczy. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/13476)w serwisie GitHub.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
