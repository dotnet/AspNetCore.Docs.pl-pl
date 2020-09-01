---
title: Rozszerzalność zarządzania kluczami w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o rozszerzalności zarządzania kluczami ochrony danych ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: db718b8d4c305b75ad52054efde6b2d03f6825ed
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153535"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="cad5a-103">Rozszerzalność zarządzania kluczami w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cad5a-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="cad5a-104">Przeczytaj sekcję [Zarządzanie kluczami](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) przed przeczytaniem tej sekcji, ponieważ objaśnia ona niektóre podstawowe koncepcje związane z tymi interfejsami API.</span><span class="sxs-lookup"><span data-stu-id="cad5a-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="cad5a-105">**Ostrzeżenie**: typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.</span><span class="sxs-lookup"><span data-stu-id="cad5a-105">**Warning**: Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="cad5a-106">Klucz</span><span class="sxs-lookup"><span data-stu-id="cad5a-106">Key</span></span>

<span data-ttu-id="cad5a-107">`IKey`Interfejs jest podstawową reprezentacją klucza w cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="cad5a-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="cad5a-108">Klucz terminu jest używany w tym miejscu w sensie abstrakcyjnym, a nie w sensie "materiału klucza kryptograficznego".</span><span class="sxs-lookup"><span data-stu-id="cad5a-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="cad5a-109">Klucz ma następujące właściwości:</span><span class="sxs-lookup"><span data-stu-id="cad5a-109">A key has the following properties:</span></span>

* <span data-ttu-id="cad5a-110">Data aktywacji, utworzenia i wygaśnięcia</span><span class="sxs-lookup"><span data-stu-id="cad5a-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="cad5a-111">Stan odwołania</span><span class="sxs-lookup"><span data-stu-id="cad5a-111">Revocation status</span></span>

* <span data-ttu-id="cad5a-112">Identyfikator klucza (identyfikator GUID)</span><span class="sxs-lookup"><span data-stu-id="cad5a-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cad5a-113">Ponadto `IKey` udostępnia metodę, `CreateEncryptor` która może służyć do tworzenia wystąpienia [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) powiązanego z tym kluczem.</span><span class="sxs-lookup"><span data-stu-id="cad5a-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cad5a-114">Ponadto `IKey` udostępnia metodę, `CreateEncryptorInstance` która może służyć do tworzenia wystąpienia [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) powiązanego z tym kluczem.</span><span class="sxs-lookup"><span data-stu-id="cad5a-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="cad5a-115">Nie istnieje interfejs API umożliwiający pobranie nieprzetworzonego materiału kryptograficznego z `IKey` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cad5a-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="cad5a-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="cad5a-116">IKeyManager</span></span>

<span data-ttu-id="cad5a-117">`IKeyManager`Interfejs reprezentuje obiekt odpowiedzialny za ogólny Magazyn kluczy, pobieranie i manipulowanie.</span><span class="sxs-lookup"><span data-stu-id="cad5a-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="cad5a-118">Ujawnia trzy operacje na wysokim poziomie:</span><span class="sxs-lookup"><span data-stu-id="cad5a-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="cad5a-119">Utwórz nowy klucz i Utrwalaj go w magazynie.</span><span class="sxs-lookup"><span data-stu-id="cad5a-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="cad5a-120">Pobierz wszystkie klucze z magazynu.</span><span class="sxs-lookup"><span data-stu-id="cad5a-120">Get all keys from storage.</span></span>

* <span data-ttu-id="cad5a-121">Odwołaj jeden lub więcej kluczy i Utrwalaj informacje o odwołaniu do magazynu.</span><span class="sxs-lookup"><span data-stu-id="cad5a-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="cad5a-122">Pisanie `IKeyManager` to bardzo zaawansowane zadanie i większość deweloperów nie powinna próbować tego.</span><span class="sxs-lookup"><span data-stu-id="cad5a-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="cad5a-123">Zamiast tego większość deweloperów powinna korzystać z udogodnień oferowanych przez klasę [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="cad5a-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="cad5a-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="cad5a-124">XmlKeyManager</span></span>

<span data-ttu-id="cad5a-125">`XmlKeyManager`Typ jest implementacją betonu w miejscu `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="cad5a-126">Zapewnia ona kilka przydatnych udogodnień, w tym klucze Escrow i szyfrowanie kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="cad5a-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="cad5a-127">Klucze w tym systemie są reprezentowane jako elementy XML (w odniesieniu do [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="cad5a-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="cad5a-128">`XmlKeyManager` zależy od kilku innych składników w trakcie wykonywania zadań:</span><span class="sxs-lookup"><span data-stu-id="cad5a-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="cad5a-129">`AlgorithmConfiguration`, która wymusza algorytmy używane przez nowe klucze.</span><span class="sxs-lookup"><span data-stu-id="cad5a-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="cad5a-130">`IXmlRepository`, która kontroluje, gdzie klucze są utrwalane w magazynie.</span><span class="sxs-lookup"><span data-stu-id="cad5a-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="cad5a-131">`IXmlEncryptor` [opcjonalne], co umożliwia szyfrowanie kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="cad5a-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="cad5a-132">`IKeyEscrowSink` [opcjonalny], który zapewnia usługi Key Escrow.</span><span class="sxs-lookup"><span data-stu-id="cad5a-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="cad5a-133">`IXmlRepository`, która kontroluje, gdzie klucze są utrwalane w magazynie.</span><span class="sxs-lookup"><span data-stu-id="cad5a-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="cad5a-134">`IXmlEncryptor` [opcjonalne], co umożliwia szyfrowanie kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="cad5a-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="cad5a-135">`IKeyEscrowSink` [opcjonalny], który zapewnia usługi Key Escrow.</span><span class="sxs-lookup"><span data-stu-id="cad5a-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="cad5a-136">Poniżej znajdują się diagramy wysokiego poziomu, które wskazują, jak te składniki są połączone ze sobą w ramach programu `XmlKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Tworzenie klucza](key-management/_static/keycreation2.png)

<span data-ttu-id="cad5a-138">*Tworzenie klucza/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="cad5a-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="cad5a-139">W implementacji `CreateNewKey` `AlgorithmConfiguration` składnik jest używany do utworzenia unikatowego `IAuthenticatedEncryptorDescriptor` , który następnie jest serializowany w formacie XML.</span><span class="sxs-lookup"><span data-stu-id="cad5a-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="cad5a-140">Jeśli istnieje klucz ujścia usługi Escrow, nieprzetworzony (niezaszyfrowany) kod XML jest dostarczany do ujścia magazynu długoterminowego.</span><span class="sxs-lookup"><span data-stu-id="cad5a-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="cad5a-141">Niezaszyfrowaną zawartość XML jest następnie uruchamiana przez `IXmlEncryptor` (w razie potrzeby) do generowania zaszyfrowanego dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="cad5a-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="cad5a-142">Ten zaszyfrowany dokument jest trwały w magazynie długoterminowym za pośrednictwem programu `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="cad5a-143">(Jeśli nie `IXmlEncryptor` jest skonfigurowany, niezaszyfrowane dokumenty są utrwalane w `IXmlRepository` .)</span><span class="sxs-lookup"><span data-stu-id="cad5a-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Pobieranie klucza](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Tworzenie klucza](key-management/_static/keycreation1.png)

<span data-ttu-id="cad5a-146">*Tworzenie klucza/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="cad5a-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="cad5a-147">W implementacji `CreateNewKey` `IAuthenticatedEncryptorConfiguration` składnik jest używany do utworzenia unikatowego `IAuthenticatedEncryptorDescriptor` , który następnie jest serializowany w formacie XML.</span><span class="sxs-lookup"><span data-stu-id="cad5a-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="cad5a-148">Jeśli istnieje klucz ujścia usługi Escrow, nieprzetworzony (niezaszyfrowany) kod XML jest dostarczany do ujścia magazynu długoterminowego.</span><span class="sxs-lookup"><span data-stu-id="cad5a-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="cad5a-149">Niezaszyfrowaną zawartość XML jest następnie uruchamiana przez `IXmlEncryptor` (w razie potrzeby) do generowania zaszyfrowanego dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="cad5a-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="cad5a-150">Ten zaszyfrowany dokument jest trwały w magazynie długoterminowym za pośrednictwem programu `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="cad5a-151">(Jeśli nie `IXmlEncryptor` jest skonfigurowany, niezaszyfrowane dokumenty są utrwalane w `IXmlRepository` .)</span><span class="sxs-lookup"><span data-stu-id="cad5a-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Pobieranie klucza](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="cad5a-153">*Pobieranie klucza/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="cad5a-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="cad5a-154">W implementacji programu `GetAllKeys` dokumenty XML reprezentujące klucze i odwołania są odczytywane z bazowego `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="cad5a-155">Jeśli te dokumenty są zaszyfrowane, system automatycznie je odszyfruje.</span><span class="sxs-lookup"><span data-stu-id="cad5a-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="cad5a-156">`XmlKeyManager` tworzy odpowiednie `IAuthenticatedEncryptorDescriptorDeserializer` wystąpienia do deserializacji dokumentów z powrotem do `IAuthenticatedEncryptorDescriptor` wystąpień, które są następnie opakowane w poszczególne `IKey` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cad5a-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="cad5a-157">Ta kolekcja `IKey` wystąpień jest zwracana do obiektu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="cad5a-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="cad5a-158">Więcej informacji o poszczególnych elementach XML można znaleźć w [dokumencie format magazynu kluczy](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="cad5a-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="cad5a-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-159">IXmlRepository</span></span>

<span data-ttu-id="cad5a-160">`IXmlRepository`Interfejs reprezentuje typ, który może utrwalać XML i pobierać XML z magazynu zapasowego.</span><span class="sxs-lookup"><span data-stu-id="cad5a-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="cad5a-161">Udostępnia dwa interfejsy API:</span><span class="sxs-lookup"><span data-stu-id="cad5a-161">It exposes two APIs:</span></span>

* <span data-ttu-id="cad5a-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="cad5a-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="cad5a-163">Implementacje `IXmlRepository` nie muszą przeanalizować XML przechodzącego przez nie.</span><span class="sxs-lookup"><span data-stu-id="cad5a-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="cad5a-164">Powinny traktować dokumenty XML jako nieprzezroczyste i pozwolić wyższym warstwom na generowanie i analizowanie dokumentów.</span><span class="sxs-lookup"><span data-stu-id="cad5a-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="cad5a-165">Istnieją cztery wbudowane typy, które implementują `IXmlRepository` :</span><span class="sxs-lookup"><span data-stu-id="cad5a-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="cad5a-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="cad5a-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="cad5a-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="cad5a-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="cad5a-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="cad5a-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="cad5a-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="cad5a-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cad5a-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="cad5a-174">Aby uzyskać więcej informacji, zobacz dokument dotyczący [dostawców magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="cad5a-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="cad5a-175">Rejestracja niestandardowa `IXmlRepository` jest odpowiednia w przypadku korzystania z innego magazynu zapasowego (np. platformy Azure Table Storage).</span><span class="sxs-lookup"><span data-stu-id="cad5a-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="cad5a-176">Aby zmienić domyślne repozytorium dla całej aplikacji, zarejestruj `IXmlRepository` wystąpienie niestandardowe:</span><span class="sxs-lookup"><span data-stu-id="cad5a-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="cad5a-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="cad5a-177">IXmlEncryptor</span></span>

<span data-ttu-id="cad5a-178">`IXmlEncryptor`Interfejs reprezentuje typ, który może zaszyfrować element XML w postaci zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="cad5a-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="cad5a-179">Udostępnia on pojedynczy interfejs API:</span><span class="sxs-lookup"><span data-stu-id="cad5a-179">It exposes a single API:</span></span>

* <span data-ttu-id="cad5a-180">Szyfruj (XElement zwykły): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="cad5a-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="cad5a-181">Jeśli Serializacja `IAuthenticatedEncryptorDescriptor` zawiera dowolne elementy oznaczone jako "wymaga szyfrowania", program uruchomi `XmlKeyManager` te elementy za pośrednictwem skonfigurowanej `IXmlEncryptor` `Encrypt` metody i będzie utrzymywać element ENCIPHERED, a nie element w postaci zwykłego tekstu `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="cad5a-182">Wyjście `Encrypt` metody jest `EncryptedXmlInfo` obiektem.</span><span class="sxs-lookup"><span data-stu-id="cad5a-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="cad5a-183">Ten obiekt jest otoką, która zawiera zarówno wynikowe ENCIPHERED `XElement` , jak i typ, który reprezentuje, `IXmlDecryptor` który może służyć do odszyfrowania odpowiadającego elementu.</span><span class="sxs-lookup"><span data-stu-id="cad5a-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="cad5a-184">Istnieją cztery wbudowane typy, które implementują `IXmlEncryptor` :</span><span class="sxs-lookup"><span data-stu-id="cad5a-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="cad5a-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="cad5a-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="cad5a-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="cad5a-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="cad5a-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="cad5a-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="cad5a-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="cad5a-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="cad5a-189">Aby uzyskać więcej informacji, zobacz sekcję [szyfrowanie kluczy w dokumencie REST](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="cad5a-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="cad5a-190">Aby zmienić domyślny mechanizm szyfrowania klucza — w przypadku aplikacji, zarejestruj `IXmlEncryptor` wystąpienie niestandardowe:</span><span class="sxs-lookup"><span data-stu-id="cad5a-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="cad5a-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="cad5a-191">IXmlDecryptor</span></span>

<span data-ttu-id="cad5a-192">`IXmlDecryptor`Interfejs reprezentuje typ, który zna sposób odszyfrowania `XElement` , który był ENCIPHERED za pośrednictwem `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="cad5a-193">Udostępnia on pojedynczy interfejs API:</span><span class="sxs-lookup"><span data-stu-id="cad5a-193">It exposes a single API:</span></span>

* <span data-ttu-id="cad5a-194">Odszyfruj (XElement encryptedelement): XElement</span><span class="sxs-lookup"><span data-stu-id="cad5a-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="cad5a-195">`Decrypt`Metoda unwykonuje szyfrowanie wykonywane przez `IXmlEncryptor.Encrypt` .</span><span class="sxs-lookup"><span data-stu-id="cad5a-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="cad5a-196">Ogólnie rzecz biorąc każda konkretna `IXmlEncryptor` implementacja będzie miała odpowiednią konkretną `IXmlDecryptor` implementację.</span><span class="sxs-lookup"><span data-stu-id="cad5a-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="cad5a-197">Typy, które implementują, `IXmlDecryptor` powinny mieć jeden z następujących dwóch konstruktorów publicznych:</span><span class="sxs-lookup"><span data-stu-id="cad5a-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="cad5a-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="cad5a-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="cad5a-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="cad5a-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="cad5a-200">`IServiceProvider`Przesłany do konstruktora może mieć wartość null.</span><span class="sxs-lookup"><span data-stu-id="cad5a-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="cad5a-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="cad5a-201">IKeyEscrowSink</span></span>

<span data-ttu-id="cad5a-202">`IKeyEscrowSink`Interfejs reprezentuje typ, który może prowadzić do Escrow informacji poufnych.</span><span class="sxs-lookup"><span data-stu-id="cad5a-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="cad5a-203">Odwołaj te serializowane deskryptory mogą zawierać poufne informacje (takie jak materiał kryptograficzny) i to, co doprowadziło do wprowadzenia typu [IXmlEncryptor](#ixmlencryptor) w pierwszym miejscu.</span><span class="sxs-lookup"><span data-stu-id="cad5a-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="cad5a-204">Jednak awarie i pierścienie kluczy można usunąć lub uszkodzić.</span><span class="sxs-lookup"><span data-stu-id="cad5a-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="cad5a-205">Interfejs Escrow zapewnia awaryjny kreskę ucieczki, umożliwiając dostęp do nieprzetworzonej serializowanej XML, zanim zostanie on przekształcony przez wszystkie skonfigurowane [IXmlEncryptor](#ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="cad5a-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="cad5a-206">Interfejs uwidacznia pojedynczy interfejs API:</span><span class="sxs-lookup"><span data-stu-id="cad5a-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="cad5a-207">Magazyn (identyfikator GUID keyId, element XElement)</span><span class="sxs-lookup"><span data-stu-id="cad5a-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="cad5a-208">Jest to `IKeyEscrowSink` implementacja do obsługi dostarczonego elementu w bezpieczny sposób spójny z zasadami biznesowymi.</span><span class="sxs-lookup"><span data-stu-id="cad5a-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="cad5a-209">Jedną z możliwych implementacji dla ujścia usługi Escrow jest zaszyfrowanie elementu XML przy użyciu znanego certyfikatu firmowy X. 509, w którym został zgłoszony klucz prywatny certyfikatu. `CertificateXmlEncryptor` ten typ może pomóc w tym.</span><span class="sxs-lookup"><span data-stu-id="cad5a-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="cad5a-210">`IKeyEscrowSink`Implementacja jest również odpowiedzialna za utrwalanie podanego elementu.</span><span class="sxs-lookup"><span data-stu-id="cad5a-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="cad5a-211">Domyślnie żaden mechanizm Escrow nie jest włączony, jednak Administratorzy serwera mogą [konfigurować to globalnie](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="cad5a-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="cad5a-212">Można go również skonfigurować programowo za pośrednictwem `IDataProtectionBuilder.AddKeyEscrowSink` metody, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="cad5a-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="cad5a-213">`AddKeyEscrowSink`Przeciążania metod dublowane `IServiceCollection.AddSingleton` i `IServiceCollection.AddInstance` przeciążenia, ponieważ `IKeyEscrowSink` wystąpienia są przeznaczone jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="cad5a-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="cad5a-214">Jeśli `IKeyEscrowSink` zarejestrowano wiele wystąpień, każda z nich zostanie wywołana podczas generowania klucza, dzięki czemu klucze mogą być jednocześnie przełączone do wielu mechanizmów.</span><span class="sxs-lookup"><span data-stu-id="cad5a-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="cad5a-215">Brak interfejsu API do odczytu materiału z `IKeyEscrowSink` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="cad5a-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="cad5a-216">Jest to zgodne z teorią konstrukcyjną mechanizmu Escrow: jest on przeznaczony do udostępnienia klucza zaufanemu Urzędowi, a ponieważ aplikacja nie jest zaufanym urzędem, nie powinna mieć dostępu do własnego materiału, w którym została zadana transakcja.</span><span class="sxs-lookup"><span data-stu-id="cad5a-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="cad5a-217">Poniższy przykładowy kod demonstruje tworzenie i rejestrowanie kluczy, w `IKeyEscrowSink` których są one objęte płatnością, aby tylko członkowie grupy "Administratorzy CONTOSODomain" mogli je odzyskać.</span><span class="sxs-lookup"><span data-stu-id="cad5a-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="cad5a-218">Aby uruchomić ten przykład, musisz być na komputerze przyłączonym do domeny systemu Windows 8/Windows Server 2012, a kontroler domeny musi być w systemie Windows Server 2012 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="cad5a-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
