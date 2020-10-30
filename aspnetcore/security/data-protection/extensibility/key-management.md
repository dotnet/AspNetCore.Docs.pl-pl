---
title: Rozszerzalność zarządzania kluczami w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o rozszerzalności zarządzania kluczami ochrony danych ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060771"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Rozszerzalność zarządzania kluczami w ASP.NET Core

Przeczytaj sekcję [Zarządzanie kluczami](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) przed przeczytaniem tej sekcji, ponieważ objaśnia ona niektóre podstawowe koncepcje związane z tymi interfejsami API.

**Ostrzeżenie** : typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.

## <a name="key"></a>Klucz

`IKey`Interfejs jest podstawową reprezentacją klucza w cryptosystem. Klucz terminu jest używany w tym miejscu w sensie abstrakcyjnym, a nie w sensie "materiału klucza kryptograficznego". Klucz ma następujące właściwości:

* Data aktywacji, utworzenia i wygaśnięcia

* Stan odwołania

* Identyfikator klucza (identyfikator GUID)

::: moniker range=">= aspnetcore-2.0"

Ponadto `IKey` udostępnia metodę, `CreateEncryptor` która może służyć do tworzenia wystąpienia [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) powiązanego z tym kluczem.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Ponadto `IKey` udostępnia metodę, `CreateEncryptorInstance` która może służyć do tworzenia wystąpienia [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) powiązanego z tym kluczem.

::: moniker-end

> [!NOTE]
> Nie istnieje interfejs API umożliwiający pobranie nieprzetworzonego materiału kryptograficznego z `IKey` wystąpienia.

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager`Interfejs reprezentuje obiekt odpowiedzialny za ogólny Magazyn kluczy, pobieranie i manipulowanie. Ujawnia trzy operacje na wysokim poziomie:

* Utwórz nowy klucz i Utrwalaj go w magazynie.

* Pobierz wszystkie klucze z magazynu.

* Odwołaj jeden lub więcej kluczy i Utrwalaj informacje o odwołaniu do magazynu.

>[!WARNING]
> Pisanie `IKeyManager` to bardzo zaawansowane zadanie i większość deweloperów nie powinna próbować tego. Zamiast tego większość deweloperów powinna korzystać z udogodnień oferowanych przez klasę [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager`Typ jest implementacją betonu w miejscu `IKeyManager` . Zapewnia ona kilka przydatnych udogodnień, w tym klucze Escrow i szyfrowanie kluczy w spoczynku. Klucze w tym systemie są reprezentowane jako elementy XML (w odniesieniu do [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` zależy od kilku innych składników w trakcie wykonywania zadań:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, która wymusza algorytmy używane przez nowe klucze.

* `IXmlRepository`, która kontroluje, gdzie klucze są utrwalane w magazynie.

* `IXmlEncryptor` [opcjonalne], co umożliwia szyfrowanie kluczy w spoczynku.

* `IKeyEscrowSink` [opcjonalny], który zapewnia usługi Key Escrow.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, która kontroluje, gdzie klucze są utrwalane w magazynie.

* `IXmlEncryptor` [opcjonalne], co umożliwia szyfrowanie kluczy w spoczynku.

* `IKeyEscrowSink` [opcjonalny], który zapewnia usługi Key Escrow.

::: moniker-end

Poniżej znajdują się diagramy wysokiego poziomu, które wskazują, jak te składniki są połączone ze sobą w ramach programu `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Tworzenie klucza](key-management/_static/keycreation2.png)

*Tworzenie klucza/CreateNewKey*

W implementacji `CreateNewKey` `AlgorithmConfiguration` składnik jest używany do utworzenia unikatowego `IAuthenticatedEncryptorDescriptor` , który następnie jest serializowany w formacie XML. Jeśli istnieje klucz ujścia usługi Escrow, nieprzetworzony (niezaszyfrowany) kod XML jest dostarczany do ujścia magazynu długoterminowego. Niezaszyfrowaną zawartość XML jest następnie uruchamiana przez `IXmlEncryptor` (w razie potrzeby) do generowania zaszyfrowanego dokumentu XML. Ten zaszyfrowany dokument jest trwały w magazynie długoterminowym za pośrednictwem programu `IXmlRepository` . (Jeśli nie `IXmlEncryptor` jest skonfigurowany, niezaszyfrowane dokumenty są utrwalane w `IXmlRepository` .)

![Pobieranie klucza](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Tworzenie klucza](key-management/_static/keycreation1.png)

*Tworzenie klucza/CreateNewKey*

W implementacji `CreateNewKey` `IAuthenticatedEncryptorConfiguration` składnik jest używany do utworzenia unikatowego `IAuthenticatedEncryptorDescriptor` , który następnie jest serializowany w formacie XML. Jeśli istnieje klucz ujścia usługi Escrow, nieprzetworzony (niezaszyfrowany) kod XML jest dostarczany do ujścia magazynu długoterminowego. Niezaszyfrowaną zawartość XML jest następnie uruchamiana przez `IXmlEncryptor` (w razie potrzeby) do generowania zaszyfrowanego dokumentu XML. Ten zaszyfrowany dokument jest trwały w magazynie długoterminowym za pośrednictwem programu `IXmlRepository` . (Jeśli nie `IXmlEncryptor` jest skonfigurowany, niezaszyfrowane dokumenty są utrwalane w `IXmlRepository` .)

![Pobieranie klucza](key-management/_static/keyretrieval1.png)

::: moniker-end

*Pobieranie klucza/GetAllKeys*

W implementacji programu `GetAllKeys` dokumenty XML reprezentujące klucze i odwołania są odczytywane z bazowego `IXmlRepository` . Jeśli te dokumenty są zaszyfrowane, system automatycznie je odszyfruje. `XmlKeyManager` tworzy odpowiednie `IAuthenticatedEncryptorDescriptorDeserializer` wystąpienia do deserializacji dokumentów z powrotem do `IAuthenticatedEncryptorDescriptor` wystąpień, które są następnie opakowane w poszczególne `IKey` wystąpienia. Ta kolekcja `IKey` wystąpień jest zwracana do obiektu wywołującego.

Więcej informacji o poszczególnych elementach XML można znaleźć w [dokumencie format magazynu kluczy](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository`Interfejs reprezentuje typ, który może utrwalać XML i pobierać XML z magazynu zapasowego. Udostępnia dwa interfejsy API:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementacje `IXmlRepository` nie muszą przeanalizować XML przechodzącego przez nie. Powinny traktować dokumenty XML jako nieprzezroczyste i pozwolić wyższym warstwom na generowanie i analizowanie dokumentów.

Istnieją cztery wbudowane typy, które implementują `IXmlRepository` :

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Aby uzyskać więcej informacji, zobacz dokument dotyczący [dostawców magazynu kluczy](xref:security/data-protection/implementation/key-storage-providers) .

Rejestracja niestandardowa `IXmlRepository` jest odpowiednia w przypadku korzystania z innego magazynu zapasowego (np. platformy Azure Table Storage).

Aby zmienić domyślne repozytorium dla całej aplikacji, zarejestruj `IXmlRepository` wystąpienie niestandardowe:

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor`Interfejs reprezentuje typ, który może zaszyfrować element XML w postaci zwykłego tekstu. Udostępnia on pojedynczy interfejs API:

* Szyfruj (XElement zwykły): EncryptedXmlInfo

Jeśli Serializacja `IAuthenticatedEncryptorDescriptor` zawiera dowolne elementy oznaczone jako "wymaga szyfrowania", program uruchomi `XmlKeyManager` te elementy za pośrednictwem skonfigurowanej `IXmlEncryptor` `Encrypt` metody i będzie utrzymywać element ENCIPHERED, a nie element w postaci zwykłego tekstu `IXmlRepository` . Wyjście `Encrypt` metody jest `EncryptedXmlInfo` obiektem. Ten obiekt jest otoką, która zawiera zarówno wynikowe ENCIPHERED `XElement` , jak i typ, który reprezentuje, `IXmlDecryptor` który może służyć do odszyfrowania odpowiadającego elementu.

Istnieją cztery wbudowane typy, które implementują `IXmlEncryptor` :

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Aby uzyskać więcej informacji, zobacz sekcję [szyfrowanie kluczy w dokumencie REST](xref:security/data-protection/implementation/key-encryption-at-rest) .

Aby zmienić domyślny mechanizm szyfrowania klucza — w przypadku aplikacji, zarejestruj `IXmlEncryptor` wystąpienie niestandardowe:

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor`Interfejs reprezentuje typ, który zna sposób odszyfrowania `XElement` , który był ENCIPHERED za pośrednictwem `IXmlEncryptor` . Udostępnia on pojedynczy interfejs API:

* Odszyfruj (XElement encryptedelement): XElement

`Decrypt`Metoda unwykonuje szyfrowanie wykonywane przez `IXmlEncryptor.Encrypt` . Ogólnie rzecz biorąc każda konkretna `IXmlEncryptor` implementacja będzie miała odpowiednią konkretną `IXmlDecryptor` implementację.

Typy, które implementują, `IXmlDecryptor` powinny mieć jeden z następujących dwóch konstruktorów publicznych:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> `IServiceProvider`Przesłany do konstruktora może mieć wartość null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink`Interfejs reprezentuje typ, który może prowadzić do Escrow informacji poufnych. Odwołaj te serializowane deskryptory mogą zawierać poufne informacje (takie jak materiał kryptograficzny) i to, co doprowadziło do wprowadzenia typu [IXmlEncryptor](#ixmlencryptor) w pierwszym miejscu. Jednak awarie i pierścienie kluczy można usunąć lub uszkodzić.

Interfejs Escrow zapewnia awaryjny kreskę ucieczki, umożliwiając dostęp do nieprzetworzonej serializowanej XML, zanim zostanie on przekształcony przez wszystkie skonfigurowane [IXmlEncryptor](#ixmlencryptor). Interfejs uwidacznia pojedynczy interfejs API:

* Magazyn (identyfikator GUID keyId, element XElement)

Jest to `IKeyEscrowSink` implementacja do obsługi dostarczonego elementu w bezpieczny sposób spójny z zasadami biznesowymi. Jedną z możliwych implementacji dla ujścia usługi Escrow jest zaszyfrowanie elementu XML przy użyciu znanego certyfikatu firmowy X. 509, w którym został zgłoszony klucz prywatny certyfikatu. `CertificateXmlEncryptor` ten typ może pomóc w tym. `IKeyEscrowSink`Implementacja jest również odpowiedzialna za utrwalanie podanego elementu.

Domyślnie żaden mechanizm Escrow nie jest włączony, jednak Administratorzy serwera mogą [konfigurować to globalnie](xref:security/data-protection/configuration/machine-wide-policy). Można go również skonfigurować programowo za pośrednictwem `IDataProtectionBuilder.AddKeyEscrowSink` metody, jak pokazano w poniższym przykładzie. `AddKeyEscrowSink`Przeciążania metod dublowane `IServiceCollection.AddSingleton` i `IServiceCollection.AddInstance` przeciążenia, ponieważ `IKeyEscrowSink` wystąpienia są przeznaczone jako pojedyncze. Jeśli `IKeyEscrowSink` zarejestrowano wiele wystąpień, każda z nich zostanie wywołana podczas generowania klucza, dzięki czemu klucze mogą być jednocześnie przełączone do wielu mechanizmów.

Brak interfejsu API do odczytu materiału z `IKeyEscrowSink` wystąpienia. Jest to zgodne z teorią konstrukcyjną mechanizmu Escrow: jest on przeznaczony do udostępnienia klucza zaufanemu Urzędowi, a ponieważ aplikacja nie jest zaufanym urzędem, nie powinna mieć dostępu do własnego materiału, w którym została zadana transakcja.

Poniższy przykładowy kod demonstruje tworzenie i rejestrowanie kluczy, w `IKeyEscrowSink` których są one objęte płatnością, aby tylko członkowie grupy "Administratorzy CONTOSODomain" mogli je odzyskać.

> [!NOTE]
> Aby uruchomić ten przykład, musisz być na komputerze przyłączonym do domeny systemu Windows 8/Windows Server 2012, a kontroler domeny musi być w systemie Windows Server 2012 lub nowszym.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
