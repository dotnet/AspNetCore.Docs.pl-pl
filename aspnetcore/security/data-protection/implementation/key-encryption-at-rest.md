---
title: Szyfrowanie kluczy w systemie Windows i platformie Azure przy użyciu ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji szyfrowania klucza ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061278"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>Szyfrowanie kluczy w systemie Windows i platformie Azure przy użyciu ASP.NET Core

System ochrony danych [domyślnie stosuje mechanizm odnajdywania](xref:security/data-protection/configuration/default-settings) , aby określić, jak klucze kryptograficzne mają być szyfrowane w stanie spoczynku. Deweloper może przesłonić mechanizm odnajdywania i ręcznie określić, jak klucze mają być szyfrowane w stanie spoczynku.

> [!WARNING]
> Jeśli określisz jawną [lokalizację trwałości klucza](xref:security/data-protection/implementation/key-storage-providers), system ochrony danych wyrejestruje domyślne szyfrowanie klucza w mechanizmie Rest. W związku z tym klucze nie są już szyfrowane w stanie spoczynku. Zalecamy [określenie jawnego mechanizmu szyfrowania klucza](xref:security/data-protection/implementation/key-encryption-at-rest) dla wdrożeń produkcyjnych. Opcje mechanizmu szyfrowania w czasie spoczynku zostały opisane w tym temacie.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Aby przechowywać klucze w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), skonfiguruj system przy użyciu [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) w `Startup` klasie:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core ochrony danych: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Dotyczy tylko wdrożeń systemu Windows.**

Gdy jest używany system Windows DPAPI, materiał klucza jest szyfrowany przy użyciu funkcji [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) przed utrwaleniem do magazynu. DPAPI to odpowiedni mechanizm szyfrowania dla danych, które nigdy nie są odczytywane poza bieżącą maszyną (Chociaż istnieje możliwość przywrócenia tych kluczy do Active Directory; zobacz [Profile DPAPI i roaming](https://support.microsoft.com/kb/309408/#6)). Aby skonfigurować klucz DPAPI — szyfrowanie w spoczynku, należy wywołać jedną z metod rozszerzenia [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Jeśli `ProtectKeysWithDpapi` jest wywoływana bez parametrów, tylko bieżące konto użytkownika systemu Windows może odszyfrować utrwalonego dzwonka klucza. Opcjonalnie możesz określić, że każde konto użytkownika na komputerze (nie tylko bieżące konto użytkownika) będzie mogło odszyfrować pierścień klucza:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certyfikat X. 509

Jeśli aplikacja jest rozłożona na wiele maszyn, warto rozpowszechnić współużytkowany certyfikat X. 509 na maszynach i skonfigurować aplikacje hostowane tak, aby używały certyfikatu do szyfrowania kluczy w spoczynku:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Ze względu na ograniczenia .NET Framework obsługiwane są tylko certyfikaty z kluczami prywatnymi CAPI. Zapoznaj się z zawartością poniżej, aby zapoznać się z możliwymi obejściami tych ograniczeń.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI — NG

**Ten mechanizm jest dostępny tylko w systemie Windows 8/Windows Server 2012 lub nowszym.**

Począwszy od systemu Windows 8, system operacyjny Windows obsługuje funkcję DPAPI-NG (nazywaną również usługą CNG DPAPI). Aby uzyskać więcej informacji, zobacz [about CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

Podmiot zabezpieczeń jest zakodowany jako reguła deskryptora ochrony. W poniższym przykładzie, który wywołuje [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), tylko użytkownik przyłączony do domeny z określonym identyfikatorem SID może odszyfrować pierścień klucza:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Istnieje również Przeciążenie bez parametrów `ProtectKeysWithDpapiNG` . Użyj tej wygodnej metody, aby określić regułę "SID = {CURRENT_ACCOUNT_SID}", gdzie *CURRENT_ACCOUNT_SID* jest identyfikatorem SID bieżącego konta użytkownika systemu Windows:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

W tym scenariuszu kontroler domeny usługi AD jest odpowiedzialny za dystrybucję kluczy szyfrowania używanych przez operacje DPAPI-NG. Użytkownik docelowy może odszyfrować zaszyfrowany ładunek z dowolnego komputera przyłączonego do domeny (pod warunkiem, że proces jest uruchomiony w ramach jego tożsamości).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Szyfrowanie oparte na certyfikatach za pomocą interfejsu DPAPI systemu Windows

Jeśli aplikacja działa w systemie Windows 8.1/Windows Server 2012 R2 lub nowszym, można użyć interfejsu DPAPI systemu Windows do wykonywania szyfrowania opartego na certyfikatach. Użyj ciągu deskryptora reguły "CERTIFICATE = HashId: odcisk PALCa", gdzie *odcisk palca* to odciskiem palca szyfrowanego algorytmem szesnastkowym certyfikatu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Wszystkie aplikacje wskazywane w tym repozytorium muszą być uruchomione w systemie Windows 8.1/Windows Server 2012 R2 lub nowszym w celu odszyfrowania kluczy.

## <a name="custom-key-encryption"></a>Szyfrowanie klucza niestandardowego

Jeśli mechanizmy wbudowane nie są odpowiednie, deweloper może określić własny mechanizm szyfrowania kluczy, dostarczając niestandardowe [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).
