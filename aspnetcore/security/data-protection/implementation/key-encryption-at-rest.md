---
title: Szyfrowanie kluczy w systemie Windows i platformie Azure przy użyciu ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji szyfrowania klucza ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061278"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="24a40-103">Szyfrowanie kluczy w systemie Windows i platformie Azure przy użyciu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24a40-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="24a40-104">System ochrony danych [domyślnie stosuje mechanizm odnajdywania](xref:security/data-protection/configuration/default-settings) , aby określić, jak klucze kryptograficzne mają być szyfrowane w stanie spoczynku.</span><span class="sxs-lookup"><span data-stu-id="24a40-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="24a40-105">Deweloper może przesłonić mechanizm odnajdywania i ręcznie określić, jak klucze mają być szyfrowane w stanie spoczynku.</span><span class="sxs-lookup"><span data-stu-id="24a40-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="24a40-106">Jeśli określisz jawną [lokalizację trwałości klucza](xref:security/data-protection/implementation/key-storage-providers), system ochrony danych wyrejestruje domyślne szyfrowanie klucza w mechanizmie Rest.</span><span class="sxs-lookup"><span data-stu-id="24a40-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="24a40-107">W związku z tym klucze nie są już szyfrowane w stanie spoczynku.</span><span class="sxs-lookup"><span data-stu-id="24a40-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="24a40-108">Zalecamy [określenie jawnego mechanizmu szyfrowania klucza](xref:security/data-protection/implementation/key-encryption-at-rest) dla wdrożeń produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="24a40-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="24a40-109">Opcje mechanizmu szyfrowania w czasie spoczynku zostały opisane w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="24a40-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="24a40-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24a40-110">Azure Key Vault</span></span>

<span data-ttu-id="24a40-111">Aby przechowywać klucze w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), skonfiguruj system przy użyciu [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="24a40-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="24a40-112">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core ochrony danych: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="24a40-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="24a40-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="24a40-113">Windows DPAPI</span></span>

<span data-ttu-id="24a40-114">**Dotyczy tylko wdrożeń systemu Windows.**</span><span class="sxs-lookup"><span data-stu-id="24a40-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="24a40-115">Gdy jest używany system Windows DPAPI, materiał klucza jest szyfrowany przy użyciu funkcji [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) przed utrwaleniem do magazynu.</span><span class="sxs-lookup"><span data-stu-id="24a40-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="24a40-116">DPAPI to odpowiedni mechanizm szyfrowania dla danych, które nigdy nie są odczytywane poza bieżącą maszyną (Chociaż istnieje możliwość przywrócenia tych kluczy do Active Directory; zobacz [Profile DPAPI i roaming](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="24a40-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="24a40-117">Aby skonfigurować klucz DPAPI — szyfrowanie w spoczynku, należy wywołać jedną z metod rozszerzenia [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :</span><span class="sxs-lookup"><span data-stu-id="24a40-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="24a40-118">Jeśli `ProtectKeysWithDpapi` jest wywoływana bez parametrów, tylko bieżące konto użytkownika systemu Windows może odszyfrować utrwalonego dzwonka klucza.</span><span class="sxs-lookup"><span data-stu-id="24a40-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="24a40-119">Opcjonalnie możesz określić, że każde konto użytkownika na komputerze (nie tylko bieżące konto użytkownika) będzie mogło odszyfrować pierścień klucza:</span><span class="sxs-lookup"><span data-stu-id="24a40-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="24a40-120">Certyfikat X. 509</span><span class="sxs-lookup"><span data-stu-id="24a40-120">X.509 certificate</span></span>

<span data-ttu-id="24a40-121">Jeśli aplikacja jest rozłożona na wiele maszyn, warto rozpowszechnić współużytkowany certyfikat X. 509 na maszynach i skonfigurować aplikacje hostowane tak, aby używały certyfikatu do szyfrowania kluczy w spoczynku:</span><span class="sxs-lookup"><span data-stu-id="24a40-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="24a40-122">Ze względu na ograniczenia .NET Framework obsługiwane są tylko certyfikaty z kluczami prywatnymi CAPI.</span><span class="sxs-lookup"><span data-stu-id="24a40-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="24a40-123">Zapoznaj się z zawartością poniżej, aby zapoznać się z możliwymi obejściami tych ograniczeń.</span><span class="sxs-lookup"><span data-stu-id="24a40-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="24a40-124">Windows DPAPI — NG</span><span class="sxs-lookup"><span data-stu-id="24a40-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="24a40-125">**Ten mechanizm jest dostępny tylko w systemie Windows 8/Windows Server 2012 lub nowszym.**</span><span class="sxs-lookup"><span data-stu-id="24a40-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="24a40-126">Począwszy od systemu Windows 8, system operacyjny Windows obsługuje funkcję DPAPI-NG (nazywaną również usługą CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="24a40-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="24a40-127">Aby uzyskać więcej informacji, zobacz [about CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="24a40-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="24a40-128">Podmiot zabezpieczeń jest zakodowany jako reguła deskryptora ochrony.</span><span class="sxs-lookup"><span data-stu-id="24a40-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="24a40-129">W poniższym przykładzie, który wywołuje [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), tylko użytkownik przyłączony do domeny z określonym identyfikatorem SID może odszyfrować pierścień klucza:</span><span class="sxs-lookup"><span data-stu-id="24a40-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="24a40-130">Istnieje również Przeciążenie bez parametrów `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="24a40-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="24a40-131">Użyj tej wygodnej metody, aby określić regułę "SID = {CURRENT_ACCOUNT_SID}", gdzie *CURRENT_ACCOUNT_SID* jest identyfikatorem SID bieżącego konta użytkownika systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="24a40-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="24a40-132">W tym scenariuszu kontroler domeny usługi AD jest odpowiedzialny za dystrybucję kluczy szyfrowania używanych przez operacje DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="24a40-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="24a40-133">Użytkownik docelowy może odszyfrować zaszyfrowany ładunek z dowolnego komputera przyłączonego do domeny (pod warunkiem, że proces jest uruchomiony w ramach jego tożsamości).</span><span class="sxs-lookup"><span data-stu-id="24a40-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="24a40-134">Szyfrowanie oparte na certyfikatach za pomocą interfejsu DPAPI systemu Windows</span><span class="sxs-lookup"><span data-stu-id="24a40-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="24a40-135">Jeśli aplikacja działa w systemie Windows 8.1/Windows Server 2012 R2 lub nowszym, można użyć interfejsu DPAPI systemu Windows do wykonywania szyfrowania opartego na certyfikatach.</span><span class="sxs-lookup"><span data-stu-id="24a40-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="24a40-136">Użyj ciągu deskryptora reguły "CERTIFICATE = HashId: odcisk PALCa", gdzie *odcisk palca* to odciskiem palca szyfrowanego algorytmem szesnastkowym certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="24a40-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="24a40-137">Wszystkie aplikacje wskazywane w tym repozytorium muszą być uruchomione w systemie Windows 8.1/Windows Server 2012 R2 lub nowszym w celu odszyfrowania kluczy.</span><span class="sxs-lookup"><span data-stu-id="24a40-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="24a40-138">Szyfrowanie klucza niestandardowego</span><span class="sxs-lookup"><span data-stu-id="24a40-138">Custom key encryption</span></span>

<span data-ttu-id="24a40-139">Jeśli mechanizmy wbudowane nie są odpowiednie, deweloper może określić własny mechanizm szyfrowania kluczy, dostarczając niestandardowe [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="24a40-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
