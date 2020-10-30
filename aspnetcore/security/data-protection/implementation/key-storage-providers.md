---
title: Dostawcy magazynu kluczy w ASP.NET Core
author: rick-anderson
description: Informacje o dostawcach magazynu kluczy w ASP.NET Core i sposobach konfigurowania lokalizacji magazynu kluczy.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 36e8bc494125d0770347ddf32390365d83a91d27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051749"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Dostawcy magazynu kluczy w ASP.NET Core

System ochrony danych [domyślnie stosuje mechanizm odnajdywania](xref:security/data-protection/configuration/default-settings) , aby określić, gdzie mają być utrwalane klucze kryptograficzne. Deweloper może zastąpić domyślny mechanizm odnajdywania i ręcznie określić lokalizację.

> [!WARNING]
> Jeśli określisz jawną lokalizację trwałości klucza, system ochrony danych wyrejestruje domyślne szyfrowanie klucza w mechanizmie REST, więc klucze nie będą już szyfrowane w stanie spoczynku. Zalecane jest, aby dodatkowo [określić jawny mechanizm szyfrowania klucza](xref:security/data-protection/implementation/key-encryption-at-rest) dla wdrożeń produkcyjnych.

## <a name="file-system"></a>System plików

Aby skonfigurować repozytorium kluczy oparte na systemie plików, wywołaj procedurę konfiguracji [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , jak pokazano poniżej. Podaj [DirectoryInfo](/dotnet/api/system.io.directoryinfo) wskazujący repozytorium, w którym powinny być przechowywane klucze:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`Może wskazywać katalog na komputerze lokalnym lub może wskazywać na folder w udziale sieciowym. Jeśli wskazujesz katalog na komputerze lokalnym (a scenariuszem jest to, że tylko aplikacje na komputerze lokalnym wymagają dostępu do tego repozytorium), rozważ użycie funkcji [DPAPI systemu Windows](xref:security/data-protection/implementation/key-encryption-at-rest) (w systemie Windows), aby zaszyfrować klucze w spoczynku. W przeciwnym razie Rozważ użycie [certyfikatu X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) , aby zaszyfrować klucze w spoczynku.

## <a name="azure-storage"></a>Azure Storage

Pakiet [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) umożliwia przechowywanie kluczy ochrony danych w usłudze Azure Blob Storage. Klucze mogą być współużytkowane przez kilka wystąpień aplikacji sieci Web. Aplikacje mogą udostępniać uwierzytelnianie cookie s lub CSRF na wielu serwerach.

Aby skonfigurować dostawcę usługi Azure Blob Storage, wywołaj jedno z przeciążeń [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Jeśli aplikacja sieci Web działa jako usługa platformy Azure, tokeny uwierzytelniania mogą być tworzone automatycznie przy użyciu [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

Zobacz [więcej szczegółów na temat konfigurowania uwierzytelniania](/azure/key-vault/service-to-service-authentication) między usługami.

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

Pakiet [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) umożliwia przechowywanie kluczy ochrony danych w pamięci podręcznej Redis. Klucze mogą być współużytkowane przez kilka wystąpień aplikacji sieci Web. Aplikacje mogą udostępniać uwierzytelnianie cookie s lub CSRF na wielu serwerach.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pakiet [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) umożliwia przechowywanie kluczy ochrony danych w pamięci podręcznej Redis. Klucze mogą być współużytkowane przez kilka wystąpień aplikacji sieci Web. Aplikacje mogą udostępniać uwierzytelnianie cookie s lub CSRF na wielu serwerach.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Aby skonfigurować Redis, wywołaj jedno z przeciążeń [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aby skonfigurować Redis, wywołaj jedno z przeciążeń [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [StackExchange. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [Przykłady ASP.NET Core ochrony](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Rejestr

**Dotyczy tylko wdrożeń systemu Windows.**

Czasami aplikacja może nie mieć dostępu do zapisu w systemie plików. Rozważmy scenariusz, w którym aplikacja działa jako konto usługi wirtualnej (na przykład tożsamość puli aplikacji *w3wp.exe* ). W takich przypadkach administrator może udostępnić klucz rejestru, który jest dostępny dla tożsamości konta usługi. Wywołaj metodę rozszerzenia [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , jak pokazano poniżej. Podaj [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) wskazujący lokalizację, w której mają być przechowywane klucze kryptograficzne:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> Zalecamy używanie funkcji [DPAPI systemu Windows](xref:security/data-protection/implementation/key-encryption-at-rest) do szyfrowania kluczy w spoczynku.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

Pakiet [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) udostępnia mechanizm przechowywania kluczy ochrony danych w bazie danych przy użyciu Entity Framework Core. `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`Pakiet NuGet należy dodać do pliku projektu, ale nie jest on częścią [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Dzięki temu pakietowi klucze mogą być współużytkowane przez wiele wystąpień aplikacji sieci Web.

Aby skonfigurować dostawcę EF Core, wywołaj metodę [PersistKeysToDbContext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Parametr generyczny `TContext` musi dziedziczyć z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i implementować [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Utwórz `DataProtectionKeys` tabelę.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W oknie **konsola Menedżera pakietów** (PMC) wykonaj następujące polecenia:

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Wykonaj następujące polecenia w powłoce poleceń:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` jest `DbContext` zdefiniowany w poprzednim przykładzie kodu. Jeśli używasz `DbContext` innej nazwy, Zastąp `DbContext` nazwę `MyKeysContext` .

`DataProtectionKeys`Klasa/jednostka przyjmuje strukturę pokazaną w poniższej tabeli.

| Właściwość/pole | Typ CLR | Typ SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` , nie null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Niestandardowe repozytorium kluczy

Jeśli mechanizmy wbudowane nie są odpowiednie, deweloper może określić własny klucz trwałości, dostarczając niestandardowy [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).
