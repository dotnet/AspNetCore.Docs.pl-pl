---
title: Dostawcy magazynu kluczy w ASP.NET Core
author: rick-anderson
description: Informacje o dostawcach magazynu kluczy w ASP.NET Core i sposobach konfigurowania lokalizacji magazynu kluczy.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: a8d38f17b066a0aa9a38b1bdfea3491f733cf1bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776880"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="671ab-103">Dostawcy magazynu kluczy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="671ab-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="671ab-104">System ochrony danych [domyślnie stosuje mechanizm odnajdywania](xref:security/data-protection/configuration/default-settings) , aby określić, gdzie mają być utrwalane klucze kryptograficzne.</span><span class="sxs-lookup"><span data-stu-id="671ab-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="671ab-105">Deweloper może zastąpić domyślny mechanizm odnajdywania i ręcznie określić lokalizację.</span><span class="sxs-lookup"><span data-stu-id="671ab-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="671ab-106">Jeśli określisz jawną lokalizację trwałości klucza, system ochrony danych wyrejestruje domyślne szyfrowanie klucza w mechanizmie REST, więc klucze nie będą już szyfrowane w stanie spoczynku.</span><span class="sxs-lookup"><span data-stu-id="671ab-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="671ab-107">Zalecane jest, aby dodatkowo [określić jawny mechanizm szyfrowania klucza](xref:security/data-protection/implementation/key-encryption-at-rest) dla wdrożeń produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="671ab-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="671ab-108">System plików</span><span class="sxs-lookup"><span data-stu-id="671ab-108">File system</span></span>

<span data-ttu-id="671ab-109">Aby skonfigurować repozytorium kluczy oparte na systemie plików, wywołaj procedurę konfiguracji [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="671ab-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="671ab-110">Podaj [DirectoryInfo](/dotnet/api/system.io.directoryinfo) wskazujący repozytorium, w którym powinny być przechowywane klucze:</span><span class="sxs-lookup"><span data-stu-id="671ab-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="671ab-111">`DirectoryInfo` Może wskazywać katalog na komputerze lokalnym lub może wskazywać na folder w udziale sieciowym.</span><span class="sxs-lookup"><span data-stu-id="671ab-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="671ab-112">Jeśli wskazujesz katalog na komputerze lokalnym (a scenariuszem jest to, że tylko aplikacje na komputerze lokalnym wymagają dostępu do tego repozytorium), rozważ użycie funkcji [DPAPI systemu Windows](xref:security/data-protection/implementation/key-encryption-at-rest) (w systemie Windows), aby zaszyfrować klucze w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="671ab-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="671ab-113">W przeciwnym razie Rozważ użycie [certyfikatu X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) , aby zaszyfrować klucze w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="671ab-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="671ab-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="671ab-114">Azure Storage</span></span>

<span data-ttu-id="671ab-115">Pakiet [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) umożliwia przechowywanie kluczy ochrony danych w usłudze Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="671ab-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="671ab-116">Klucze mogą być współużytkowane przez kilka wystąpień aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="671ab-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="671ab-117">Aplikacje mogą udostępniać pliki cookie uwierzytelniania lub ochronę CSRF na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="671ab-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="671ab-118">Aby skonfigurować dostawcę usługi Azure Blob Storage, wywołaj jedno z przeciążeń [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="671ab-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="671ab-119">Jeśli aplikacja sieci Web działa jako usługa platformy Azure, tokeny uwierzytelniania mogą być tworzone automatycznie przy użyciu [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="671ab-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

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

<span data-ttu-id="671ab-120">Zobacz [więcej szczegółów na temat konfigurowania uwierzytelniania](/azure/key-vault/service-to-service-authentication) między usługami.</span><span class="sxs-lookup"><span data-stu-id="671ab-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="671ab-121">Redis</span><span class="sxs-lookup"><span data-stu-id="671ab-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="671ab-122">Pakiet [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) umożliwia przechowywanie kluczy ochrony danych w pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="671ab-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="671ab-123">Klucze mogą być współużytkowane przez kilka wystąpień aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="671ab-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="671ab-124">Aplikacje mogą udostępniać pliki cookie uwierzytelniania lub ochronę CSRF na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="671ab-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="671ab-125">Pakiet [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) umożliwia przechowywanie kluczy ochrony danych w pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="671ab-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="671ab-126">Klucze mogą być współużytkowane przez kilka wystąpień aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="671ab-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="671ab-127">Aplikacje mogą udostępniać pliki cookie uwierzytelniania lub ochronę CSRF na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="671ab-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="671ab-128">Aby skonfigurować Redis, wywołaj jedno z przeciążeń [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="671ab-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="671ab-129">Aby skonfigurować Redis, wywołaj jedno z przeciążeń [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="671ab-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="671ab-130">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="671ab-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="671ab-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="671ab-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="671ab-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="671ab-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="671ab-133">Przykłady ASP.NET Core ochrony</span><span class="sxs-lookup"><span data-stu-id="671ab-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="671ab-134">Rejestr</span><span class="sxs-lookup"><span data-stu-id="671ab-134">Registry</span></span>

<span data-ttu-id="671ab-135">**Dotyczy tylko wdrożeń systemu Windows.**</span><span class="sxs-lookup"><span data-stu-id="671ab-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="671ab-136">Czasami aplikacja może nie mieć dostępu do zapisu w systemie plików.</span><span class="sxs-lookup"><span data-stu-id="671ab-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="671ab-137">Rozważmy scenariusz, w którym aplikacja działa jako konto usługi wirtualnej (na przykład tożsamość puli aplikacji *w3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="671ab-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="671ab-138">W takich przypadkach administrator może udostępnić klucz rejestru, który jest dostępny dla tożsamości konta usługi.</span><span class="sxs-lookup"><span data-stu-id="671ab-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="671ab-139">Wywołaj metodę rozszerzenia [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="671ab-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="671ab-140">Podaj [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) wskazujący lokalizację, w której mają być przechowywane klucze kryptograficzne:</span><span class="sxs-lookup"><span data-stu-id="671ab-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="671ab-141">Zalecamy używanie funkcji [DPAPI systemu Windows](xref:security/data-protection/implementation/key-encryption-at-rest) do szyfrowania kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="671ab-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="671ab-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="671ab-142">Entity Framework Core</span></span>

<span data-ttu-id="671ab-143">Pakiet [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) udostępnia mechanizm przechowywania kluczy ochrony danych w bazie danych przy użyciu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="671ab-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="671ab-144">Pakiet `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet należy dodać do pliku projektu, ale nie jest on częścią [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="671ab-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="671ab-145">Dzięki temu pakietowi klucze mogą być współużytkowane przez wiele wystąpień aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="671ab-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="671ab-146">Aby skonfigurować dostawcę EF Core, wywołaj metodę [>\<PersistKeysToDbContext TContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="671ab-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="671ab-147">Parametr `TContext`generyczny musi dziedziczyć z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i implementować [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="671ab-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="671ab-148">Utwórz `DataProtectionKeys` tabelę.</span><span class="sxs-lookup"><span data-stu-id="671ab-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="671ab-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="671ab-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="671ab-150">W oknie **konsola Menedżera pakietów** (PMC) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="671ab-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="671ab-151">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="671ab-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="671ab-152">Wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="671ab-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="671ab-153">`MyKeysContext`jest `DbContext` zdefiniowany w poprzednim przykładzie kodu.</span><span class="sxs-lookup"><span data-stu-id="671ab-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="671ab-154">Jeśli używasz `DbContext` innej nazwy, Zastąp `DbContext` nazwę. `MyKeysContext`</span><span class="sxs-lookup"><span data-stu-id="671ab-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="671ab-155">`DataProtectionKeys` Klasa/jednostka przyjmuje strukturę pokazaną w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="671ab-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="671ab-156">Właściwość/pole</span><span class="sxs-lookup"><span data-stu-id="671ab-156">Property/Field</span></span> | <span data-ttu-id="671ab-157">Typ CLR</span><span class="sxs-lookup"><span data-stu-id="671ab-157">CLR Type</span></span> | <span data-ttu-id="671ab-158">Typ SQL</span><span class="sxs-lookup"><span data-stu-id="671ab-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="671ab-159">`int`, PK, not null</span><span class="sxs-lookup"><span data-stu-id="671ab-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="671ab-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="671ab-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="671ab-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="671ab-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="671ab-162">Niestandardowe repozytorium kluczy</span><span class="sxs-lookup"><span data-stu-id="671ab-162">Custom key repository</span></span>

<span data-ttu-id="671ab-163">Jeśli mechanizmy wbudowane nie są odpowiednie, deweloper może określić własny klucz trwałości, dostarczając niestandardowy [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="671ab-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
