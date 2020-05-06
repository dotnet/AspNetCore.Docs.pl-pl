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
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="7f60e-103">Konfigurowanie ochrony danych ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f60e-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="7f60e-104">Gdy system ochrony danych jest zainicjowany, stosuje [Ustawienia domyślne](xref:security/data-protection/configuration/default-settings) w oparciu o środowisko operacyjne.</span><span class="sxs-lookup"><span data-stu-id="7f60e-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="7f60e-105">Te ustawienia są zwykle odpowiednie dla aplikacji uruchomionych na pojedynczym komputerze.</span><span class="sxs-lookup"><span data-stu-id="7f60e-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="7f60e-106">Istnieją przypadki, w których deweloper może chcieć zmienić ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="7f60e-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="7f60e-107">Aplikacja jest rozłożona na wiele maszyn.</span><span class="sxs-lookup"><span data-stu-id="7f60e-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="7f60e-108">Ze względów zgodności.</span><span class="sxs-lookup"><span data-stu-id="7f60e-108">For compliance reasons.</span></span>

<span data-ttu-id="7f60e-109">W tych scenariuszach system ochrony danych oferuje bogaty interfejs API konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7f60e-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="7f60e-110">Podobnie jak w przypadku plików konfiguracji pierścień klucza ochrony danych powinien być chroniony przy użyciu odpowiednich uprawnień.</span><span class="sxs-lookup"><span data-stu-id="7f60e-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="7f60e-111">Możesz zaszyfrować klucze w spoczynku, ale nie uniemożliwi to osobom atakującym tworzenie nowych kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="7f60e-112">W związku z tym ma to wpływ na zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f60e-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="7f60e-113">Dostęp do lokalizacji magazynu skonfigurowanej przy użyciu ochrony danych powinien być ograniczony do samej aplikacji, podobnie jak w przypadku ochrony plików konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7f60e-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="7f60e-114">Na przykład jeśli zdecydujesz się na przechowywanie magazynu kluczy na dysku, użyj uprawnień systemu plików.</span><span class="sxs-lookup"><span data-stu-id="7f60e-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="7f60e-115">Upewnij się, że tylko tożsamość, w ramach której działa aplikacja sieci Web, ma uprawnienia do odczytu, zapisu i tworzenia dostępu do tego katalogu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="7f60e-116">W przypadku korzystania z usługi Azure Blob Storage tylko aplikacja sieci Web powinna mieć możliwość odczytywania, zapisywania lub tworzenia nowych wpisów w magazynie obiektów BLOB itd.</span><span class="sxs-lookup"><span data-stu-id="7f60e-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="7f60e-117">Metoda rozszerzająca [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) zwraca [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="7f60e-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="7f60e-118">`IDataProtectionBuilder`Opisuje metody rozszerzające, które można połączyć ze sobą w celu skonfigurowania opcji ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="7f60e-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f60e-119">W przypadku rozszerzeń ochrony danych używanych w tym artykule wymagane są następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="7f60e-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="7f60e-120">Microsoft. AspNetCore. dataprotection. AzureStorage</span><span class="sxs-lookup"><span data-stu-id="7f60e-120">Microsoft.AspNetCore.DataProtection.AzureStorage</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [<span data-ttu-id="7f60e-121">Microsoft. AspNetCore. dataprotection. AzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="7f60e-121">Microsoft.AspNetCore.DataProtection.AzureKeyVault</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="7f60e-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="7f60e-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="7f60e-123">Aby przechowywać klucze w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), skonfiguruj system przy użyciu [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="7f60e-123">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="7f60e-124">Ustaw lokalizację magazynu kluczy (na przykład [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="7f60e-124">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="7f60e-125">Należy ustawić lokalizację, ponieważ wywołuje `ProtectKeysWithAzureKeyVault` implementację [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , która wyłącza ustawienia automatycznego ochrony danych, w tym lokalizację magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-125">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="7f60e-126">W powyższym przykładzie używa się usługi Azure Blob Storage, aby zachować pierścień kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-126">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="7f60e-127">Aby uzyskać więcej informacji, zobacz [dostawcy magazynu kluczy: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="7f60e-127">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="7f60e-128">Możesz również utrwalać pierścień kluczy lokalnie za pomocą [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="7f60e-128">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="7f60e-129">`keyIdentifier` Jest to identyfikator klucza magazynu kluczy używany do szyfrowania klucza.</span><span class="sxs-lookup"><span data-stu-id="7f60e-129">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="7f60e-130">Na przykład klucz utworzony w magazynie kluczy o nazwie `dataprotection` w `contosokeyvault` ma identyfikator `https://contosokeyvault.vault.azure.net/keys/dataprotection/`klucza.</span><span class="sxs-lookup"><span data-stu-id="7f60e-130">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="7f60e-131">Podaj aplikację z **odwinięciem klucza** i **zawiń uprawnienia klucza** do magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-131">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="7f60e-132">`ProtectKeysWithAzureKeyVault`przeciążenia</span><span class="sxs-lookup"><span data-stu-id="7f60e-132">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="7f60e-133">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) zezwala na użycie [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) , aby umożliwić systemowi ochrony danych korzystanie z magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-133">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="7f60e-134">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) zezwala na użycie a `ClientId` i [certyfikatu x509](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) , aby umożliwić systemowi ochrony danych korzystanie z magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-134">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="7f60e-135">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) umożliwia korzystanie z programu `ClientId` i `ClientSecret` , aby umożliwić systemowi ochrony danych korzystanie z magazynu kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-135">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="7f60e-136">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="7f60e-136">PersistKeysToFileSystem</span></span>

<span data-ttu-id="7f60e-137">Aby przechowywać klucze w udziale UNC zamiast w domyślnej lokalizacji *% LocalAppData%* , skonfiguruj system przy użyciu [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="7f60e-137">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="7f60e-138">Jeśli zmienisz lokalizację trwałości klucza, system przestanie automatycznie szyfrować klucze w spoczynku, ponieważ nie wie, czy DPAPI jest odpowiednim mechanizmem szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="7f60e-138">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="7f60e-139">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="7f60e-139">ProtectKeysWith\*</span></span>

<span data-ttu-id="7f60e-140">System można skonfigurować tak, aby chronił klucze w spoczynku przez wywołanie dowolnego interfejsu API [konfiguracji\* ProtectKeysWith](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-140">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="7f60e-141">Rozważmy poniższy przykład, który przechowuje klucze w udziale UNC i szyfruje te klucze przy użyciu określonego certyfikatu X. 509:</span><span class="sxs-lookup"><span data-stu-id="7f60e-141">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7f60e-142">W ASP.NET Core 2,1 lub nowszej można podać [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) do [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), takie jak certyfikat załadowany z pliku:</span><span class="sxs-lookup"><span data-stu-id="7f60e-142">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="7f60e-143">Zapoznaj się z tematem [szyfrowanie kluczy w spoczynku](xref:security/data-protection/implementation/key-encryption-at-rest) , aby uzyskać więcej przykładów i dyskusji na temat wbudowanych mechanizmów szyfrowania kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-143">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="7f60e-144">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="7f60e-144">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="7f60e-145">W ASP.NET Core 2,1 lub nowszych można obrócić certyfikaty i odszyfrować klucze w spoczynku przy użyciu tablicy [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) Certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="7f60e-145">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="7f60e-146">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="7f60e-146">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="7f60e-147">Aby skonfigurować system do używania okresu istnienia klucza wynoszącego 14 dni zamiast domyślnego 90 dni, należy użyć [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="7f60e-147">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="7f60e-148">Setapplicationname</span><span class="sxs-lookup"><span data-stu-id="7f60e-148">SetApplicationName</span></span>

<span data-ttu-id="7f60e-149">Domyślnie system ochrony danych izoluje aplikacje od siebie w oparciu o ich ścieżki [Główne zawartości](xref:fundamentals/index#content-root) , nawet jeśli korzystają one z tego samego repozytorium klucza fizycznego.</span><span class="sxs-lookup"><span data-stu-id="7f60e-149">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="7f60e-150">Zapobiega to zrozumieniu przez aplikacje innych chronionych ładunków.</span><span class="sxs-lookup"><span data-stu-id="7f60e-150">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="7f60e-151">Aby udostępnić chronione ładunki między aplikacjami:</span><span class="sxs-lookup"><span data-stu-id="7f60e-151">To share protected payloads among apps:</span></span>

* <span data-ttu-id="7f60e-152">Skonfiguruj <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> w każdej aplikacji o tej samej wartości.</span><span class="sxs-lookup"><span data-stu-id="7f60e-152">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="7f60e-153">Użyj tej samej wersji stosu interfejsu API ochrony danych w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="7f60e-153">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="7f60e-154">Wykonaj **jedną** z następujących czynności w plikach projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f60e-154">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="7f60e-155">Odwołuje się do tej samej udostępnionej wersji platformy za pośrednictwem [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7f60e-155">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="7f60e-156">Odwołuje się do tej samej wersji [pakietu ochrony danych](xref:security/data-protection/introduction#package-layout) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-156">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="7f60e-157">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="7f60e-157">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="7f60e-158">Może wystąpić scenariusz, w którym aplikacja nie powinna automatycznie rzutować kluczy (utworzyć nowe klucze), ponieważ zbliżają się one do wygaśnięcia.</span><span class="sxs-lookup"><span data-stu-id="7f60e-158">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="7f60e-159">Przykładem mogą być aplikacje skonfigurowane w relacji głównej/pomocniczej, w której tylko podstawowa aplikacja jest odpowiedzialna za problemy związane z zarządzaniem kluczami, a aplikacje pomocnicze po prostu mają widok tylko do odczytu dzwonka klucza.</span><span class="sxs-lookup"><span data-stu-id="7f60e-159">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="7f60e-160">Aplikacje pomocnicze można skonfigurować do traktowania pierścienia kluczy jako tylko do odczytu przez skonfigurowanie systemu przy użyciu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span><span class="sxs-lookup"><span data-stu-id="7f60e-160">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="7f60e-161">Izolacja na poziomie aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f60e-161">Per-application isolation</span></span>

<span data-ttu-id="7f60e-162">Gdy system ochrony danych jest dostarczany przez ASP.NET Core hosta, automatycznie izoluje aplikacje, nawet jeśli te aplikacje działają w ramach tego samego konta procesu roboczego i korzystają z tego samego głównego materiału klucza.</span><span class="sxs-lookup"><span data-stu-id="7f60e-162">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="7f60e-163">Jest to nieco podobne do modyfikatora IsolateApps z `<machineKey>` elementu System. Web.</span><span class="sxs-lookup"><span data-stu-id="7f60e-163">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="7f60e-164">Mechanizm izolacji działa, biorąc pod uwagę każdą aplikację na komputerze lokalnym jako unikatową dzierżawę, a <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> tym samym w przypadku każdej z tych aplikacji automatycznie dołączenie identyfikatora aplikacji jako rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="7f60e-164">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="7f60e-165">Unikatowy identyfikator aplikacji jest ścieżką fizyczną aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f60e-165">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="7f60e-166">W przypadku aplikacji hostowanych w usługach IIS unikatowym IDENTYFIKATORem jest ścieżka fizyczna programu IIS aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f60e-166">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="7f60e-167">Jeśli aplikacja jest wdrażana w środowisku kolektywu serwerów sieci Web, ta wartość jest stabilna przy założeniu, że środowiska IIS są skonfigurowane w podobny sposób na wszystkich komputerach w kolektywie serwerów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7f60e-167">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="7f60e-168">W przypadku aplikacji, które są uruchomione na [serwerze Kestrel](xref:fundamentals/servers/index#kestrel), UNIKATOWYm identyfikatorem jest ścieżka fizyczna do aplikacji na dysku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-168">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="7f60e-169">Unikatowy identyfikator został zaprojektowany w celu przerobienia&mdash;resetuje zarówno poszczególne aplikacje, jak i maszyny.</span><span class="sxs-lookup"><span data-stu-id="7f60e-169">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="7f60e-170">Ten mechanizm izolacji zakłada, że aplikacje nie są złośliwe.</span><span class="sxs-lookup"><span data-stu-id="7f60e-170">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="7f60e-171">Złośliwa aplikacja zawsze ma wpływ na każdą inną aplikację uruchomioną w ramach tego samego konta procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="7f60e-171">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="7f60e-172">W udostępnionym środowisku hostingu w przypadku, gdy aplikacje są wzajemnie niezaufane, dostawca hostingu powinien wykonać kroki, aby zapewnić izolację na poziomie systemu operacyjnego między aplikacjami, w tym oddzielić repozytoria klucza podstawowego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f60e-172">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="7f60e-173">Jeśli system ochrony danych nie jest dostarczany przez hosta ASP.NET Core (na przykład w przypadku wystąpienia go za pośrednictwem `DataProtectionProvider` konkretnego typu) izolacja aplikacji jest domyślnie wyłączona.</span><span class="sxs-lookup"><span data-stu-id="7f60e-173">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="7f60e-174">Gdy izolacja aplikacji jest wyłączona, wszystkie aplikacje obsługiwane przez ten sam materiał klucza mogą współużytkować ładunki, o ile będą one miały odpowiednie [cele](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="7f60e-174">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="7f60e-175">Aby zapewnić izolację aplikacji w tym środowisku, wywołaj metodę [Setapplicationname](#setapplicationname) w obiekcie Configuration i podaj unikatową nazwę dla każdej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f60e-175">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="7f60e-176">Zmienianie algorytmów za pomocą UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="7f60e-176">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="7f60e-177">Stos ochrony danych pozwala zmienić domyślny algorytm używany przez nowo generowane klucze.</span><span class="sxs-lookup"><span data-stu-id="7f60e-177">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="7f60e-178">Najprostszym sposobem wykonania tej czynności jest wywołanie [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) z wywołania zwrotnego konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="7f60e-178">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="7f60e-179">Domyślnym EncryptionAlgorithm jest AES-256-CBC, a domyślnym ValidationAlgorithm jest HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="7f60e-179">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="7f60e-180">Zasady domyślne mogą być ustawiane przez administratora systemu za pośrednictwem [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy), ale jawne wywołanie `UseCryptographicAlgorithms` przesłania zasady domyślne.</span><span class="sxs-lookup"><span data-stu-id="7f60e-180">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="7f60e-181">Wywołanie `UseCryptographicAlgorithms` pozwala określić żądany algorytm ze wstępnie zdefiniowanej wbudowanej listy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-181">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="7f60e-182">Nie musisz martwić się o implementację algorytmu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-182">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="7f60e-183">W powyższym scenariuszu system ochrony danych próbuje użyć implementacji CNG w przypadku uruchamiania w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="7f60e-183">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="7f60e-184">W przeciwnym razie powraca do zarządzanej klasy [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-184">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="7f60e-185">Można ręcznie określić implementację za pośrednictwem wywołania [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="7f60e-185">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="7f60e-186">Zmiana algorytmów nie ma wpływu na istniejące klucze w pęku kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-186">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="7f60e-187">Ma to wpływ tylko na nowo wygenerowane klucze.</span><span class="sxs-lookup"><span data-stu-id="7f60e-187">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="7f60e-188">Określanie niestandardowych algorytmów zarządzanych</span><span class="sxs-lookup"><span data-stu-id="7f60e-188">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7f60e-189">Aby określić niestandardowe algorytmy zarządzane, Utwórz wystąpienie [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) , które wskazuje na typy implementacji:</span><span class="sxs-lookup"><span data-stu-id="7f60e-189">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="7f60e-190">Aby określić niestandardowe algorytmy zarządzane, Utwórz wystąpienie [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) , które wskazuje na typy implementacji:</span><span class="sxs-lookup"><span data-stu-id="7f60e-190">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="7f60e-191">Ogólnie rzecz \*biorąc, właściwości typu muszą wskazywać na konkretny, instantiable (za pośrednictwem publicznego elementu ctor bez parametrów) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) i [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), chociaż system specjalne uwzględnia pewne wartości `typeof(Aes)` , takie jak dla wygody.</span><span class="sxs-lookup"><span data-stu-id="7f60e-191">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="7f60e-192">SymmetricAlgorithm musi mieć długość klucza wynoszącą ≥ 128 bitów i rozmiar bloku wynoszący ≥ 64 bitów i musi obsługiwać szyfrowanie w trybie CBC z dopełnieniem #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-192">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="7f60e-193">KeyedHashAlgorithm musi mieć rozmiar podsumowania >= 128 bitów i musi obsługiwać klucze długości równe długości skrótu algorytmu wyznaczania wartości skrótu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-193">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="7f60e-194">KeyedHashAlgorithm nie jest ściśle wymagana jako HMAC.</span><span class="sxs-lookup"><span data-stu-id="7f60e-194">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="7f60e-195">Określanie niestandardowych algorytmów CNG systemu Windows</span><span class="sxs-lookup"><span data-stu-id="7f60e-195">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7f60e-196">Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania w trybie CBC z walidacją HMAC, Utwórz wystąpienie [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) zawierające informacje o algorytmach:</span><span class="sxs-lookup"><span data-stu-id="7f60e-196">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="7f60e-197">Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania w trybie CBC z walidacją HMAC, Utwórz wystąpienie [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) zawierające informacje o algorytmach:</span><span class="sxs-lookup"><span data-stu-id="7f60e-197">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="7f60e-198">Algorytm szyfrowania bloku symetrycznego musi mieć długość klucza wynoszącą >= 128 bitów, rozmiar bloku >= 64 bitów i musi obsługiwać szyfrowanie w trybie CBC z dopełnieniem #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-198">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="7f60e-199">Algorytm wyznaczania wartości skrótu musi mieć rozmiar Digest >= 128 bitów i musi być obsługiwany przy użyciu flagi\_flagi\_alg\_uchwytu HMAC\_(BCRYPT).</span><span class="sxs-lookup"><span data-stu-id="7f60e-199">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="7f60e-200">Właściwości \*dostawcy można ustawić na wartość null, aby użyć domyślnego dostawcy dla określonego algorytmu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-200">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="7f60e-201">Aby uzyskać więcej informacji, zobacz dokumentację [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-201">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7f60e-202">Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania Galois/counter z walidacją, Utwórz wystąpienie [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) zawierające informacje o algorytmach:</span><span class="sxs-lookup"><span data-stu-id="7f60e-202">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="7f60e-203">Aby określić niestandardowy algorytm Windows CNG przy użyciu szyfrowania Galois/counter z walidacją, Utwórz wystąpienie [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) zawierające informacje o algorytmach:</span><span class="sxs-lookup"><span data-stu-id="7f60e-203">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="7f60e-204">Algorytm szyfrowania bloku symetrycznego musi mieć długość klucza wynoszącą >= 128 bitów, rozmiar bloku równy 128 bitów i musi obsługiwać szyfrowanie GCM.</span><span class="sxs-lookup"><span data-stu-id="7f60e-204">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="7f60e-205">Właściwość [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) można ustawić na wartość null, aby użyć domyślnego dostawcy dla określonego algorytmu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-205">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="7f60e-206">Aby uzyskać więcej informacji, zobacz dokumentację [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-206">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="7f60e-207">Określanie innych algorytmów niestandardowych</span><span class="sxs-lookup"><span data-stu-id="7f60e-207">Specifying other custom algorithms</span></span>

<span data-ttu-id="7f60e-208">Chociaż nie jest udostępniany jako interfejs API pierwszej klasy, system ochrony danych jest wystarczająco rozszerzalny, aby można było określić niemal dowolny rodzaj algorytmu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-208">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="7f60e-209">Na przykład można zachować wszystkie klucze zawarte w sprzętowym module zabezpieczeń (HSM) i zapewnić niestandardową implementację podstawowych procedur szyfrowania i odszyfrowywania.</span><span class="sxs-lookup"><span data-stu-id="7f60e-209">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="7f60e-210">Aby uzyskać więcej informacji, zobacz [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) z [rozszerzalnością kryptografii Core](xref:security/data-protection/extensibility/core-crypto) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-210">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="7f60e-211">Utrwalanie kluczy podczas hostowania w kontenerze platformy Docker</span><span class="sxs-lookup"><span data-stu-id="7f60e-211">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="7f60e-212">W przypadku hostowania w kontenerze [platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) klucze powinny być utrzymywane w obu:</span><span class="sxs-lookup"><span data-stu-id="7f60e-212">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="7f60e-213">Folder, który jest woluminem platformy Docker, który utrzymuje się poza okresem istnienia kontenera, takim jak udostępniony wolumin lub wolumin zainstalowany na hoście.</span><span class="sxs-lookup"><span data-stu-id="7f60e-213">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="7f60e-214">Dostawca zewnętrzny, taki jak [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) lub [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="7f60e-214">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="7f60e-215">Utrwalanie kluczy za pomocą Redis</span><span class="sxs-lookup"><span data-stu-id="7f60e-215">Persisting keys with Redis</span></span>

<span data-ttu-id="7f60e-216">Tylko wersje Redis obsługujące [trwałość danych Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) powinny być używane do przechowywania kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-216">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="7f60e-217">[Magazyn obiektów blob platformy Azure](/azure/storage/blobs/storage-blobs-introduction) jest trwały i może służyć do przechowywania kluczy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-217">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="7f60e-218">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/13476)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="7f60e-218">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f60e-219">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7f60e-219">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
