---
title: Rozszerzalność kryptografii Core w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer i fabryki najwyższego poziomu.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 4c802bc4beb1f1fde812e6c3f55fc43b5d569b66
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635323"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Rozszerzalność kryptografii Core w ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

Interfejs **IAuthenticatedEncryptor** jest podstawowym blokiem konstrukcyjnym podsystemu kryptograficznego. Istnieje zwykle jeden IAuthenticatedEncryptor na klucz, a wystąpienie IAuthenticatedEncryptor otacza cały materiał klucza kryptograficznego i informacje o algorytmach, które są niezbędne do wykonania operacji kryptograficznych.

Jako że nazwa sugeruje, typ jest odpowiedzialny za dostarczanie uwierzytelnianych usług szyfrowania i odszyfrowywania. Udostępnia następujące dwa interfejsy API.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Metoda szyfrowania zwraca obiekt BLOB, który zawiera zwykły tekst ENCIPHERED oraz tag uwierzytelniania. Tag uwierzytelniania musi obejmować dodatkowe dane uwierzytelnione (AAD), ale w przypadku samej usługi AAD nie należy odzyskać jej od końcowego ładunku. Metoda odszyfrowuje weryfikuje tag uwierzytelniania i zwraca odszyfrowany ładunek. Wszystkie błędy (z wyjątkiem ArgumentNullException i podobne) powinny być dokładnie do CryptographicException.

> [!NOTE]
> Samo wystąpienie IAuthenticatedEncryptor nie musi zawierać materiału klucza. Na przykład implementacja może delegować do modułu HSM dla wszystkich operacji.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Jak utworzyć IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

Interfejs **IAuthenticatedEncryptorFactory** reprezentuje typ, który wie, jak utworzyć wystąpienie [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Jego interfejs API jest następujący.

* CreateEncryptorInstance (klucz IKey): IAuthenticatedEncryptor

W przypadku dowolnego wystąpienia IKey wszystkie uwierzytelnione, wywołane przez nią metody CreateEncryptorInstance, powinny być uważane za równoważne, jak w poniższym przykładzie kodu.

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

Interfejs **IAuthenticatedEncryptorDescriptor** reprezentuje typ, który wie, jak utworzyć wystąpienie [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Jego interfejs API jest następujący.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Podobnie jak w przypadku IAuthenticatedEncryptor, zakłada się, że wystąpienie IAuthenticatedEncryptorDescriptor jest zawijane według jednego określonego klucza. Oznacza to, że dla każdego wystąpienia IAuthenticatedEncryptorDescriptor wszystkie uwierzytelnione szyfrujące utworzone za pomocą jego metody CreateEncryptorInstance powinny być uważane za równoważne, jak w poniższym przykładzie kodu.

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (tylko ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

Interfejs **IAuthenticatedEncryptorDescriptor** reprezentuje typ, który wie, jak eksportować sam do formatu XML. Jego interfejs API jest następujący.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serializacji XML

Podstawowa różnica między IAuthenticatedEncryptor i IAuthenticatedEncryptorDescriptor polega na tym, że deskryptor zna sposób tworzenia modułu szyfrującego i dostarczania go z prawidłowymi argumentami. Rozważmy IAuthenticatedEncryptor, której implementacja opiera się na SymmetricAlgorithm i KeyedHashAlgorithm. Zadanie modułu szyfrującego ma używać tych typów, ale nie musi wiedzieć, gdzie pochodzą z tego typu, dlatego nie można w rzeczywistości napisać poprawnego opisu sposobu jego ponownego utworzenia, gdy aplikacja zostanie uruchomiona ponownie. Deskryptor zachowuje się na wyższym poziomie. Ponieważ deskryptor wie, jak utworzyć wystąpienie modułu szyfrującego (np. Informacje o sposobie tworzenia wymaganych algorytmów), można zserializować tę wiedzę w formularzu XML, tak aby wystąpienie modułu szyfrującego można odtworzyć po zresetowaniu aplikacji.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Deskryptor może być serializowany za pośrednictwem jego procedury ExportToXml. Ta procedura zwraca element XmlSerializedDescriptorInfo, który zawiera dwie właściwości: XElement reprezentację deskryptora i typ, który reprezentuje [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) , który może być używany do przywracania aktywności tego deskryptora pod kątem odpowiedniego elementu XElement.

Deskryptor serializowany może zawierać informacje poufne, takie jak materiał klucza kryptograficznego. System ochrony danych ma wbudowaną obsługę szyfrowania informacji przed utrwaleniem ich w magazynie. Aby skorzystać z tego, deskryptor powinien oznaczać element, który zawiera poufne informacje o nazwie atrybutu "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> "), wartość "true".

>[!TIP]
> Istnieje interfejs API pomocnika służący do ustawiania tego atrybutu. Wywołaj metodę rozszerzenia XElement. MarkAsRequiresEncryption () znajdującą się w przestrzeni nazw Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Mogą również wystąpić przypadki, w których serializowany deskryptor nie zawiera informacji poufnych. Rozważ ponowne uwzględnienie wielkości liter klucza kryptograficznego przechowywanego w module HSM. Deskryptor nie może zapisywać materiału klucza podczas serializowania siebie, ponieważ moduł HSM nie ujawnia materiału w postaci zwykłego tekstu. Zamiast tego deskryptora może napisać wersję klucza zapakowanego klucza (Jeśli moduł HSM umożliwia eksport w ten sposób) lub własny unikatowy identyfikator modułu HSM.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

Interfejs **IAuthenticatedEncryptorDescriptorDeserializer** reprezentuje typ, który wie, jak deserializować wystąpienie IAuthenticatedEncryptorDescriptor z XElement. Uwidacznia on jedną metodę:

* ImportFromXml (element XElement): IAuthenticatedEncryptorDescriptor

Metoda ImportFromXml przyjmuje XElement, który został zwrócony przez [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) i tworzy odpowiednik oryginalnego elementu IAuthenticatedEncryptorDescriptor.

Typy, które implementują IAuthenticatedEncryptorDescriptorDeserializer powinny mieć jeden z następujących dwóch konstruktorów publicznych:

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> IServiceProvider przesłany do konstruktora może mieć wartość null.

## <a name="the-top-level-factory"></a>Fabryka najwyższego poziomu

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

Klasa **AlgorithmConfiguration** reprezentuje typ, który wie, jak tworzyć wystąpienia [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Udostępnia on pojedynczy interfejs API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Pomyśl o AlgorithmConfiguration jako fabryki najwyższego poziomu. Konfiguracja służy jako szablon. Powoduje Zawijanie informacji o algorytmach (np. Ta konfiguracja generuje deskryptory z kluczem głównym AES-128-GCM), ale nie jest jeszcze skojarzona z określonym kluczem.

Gdy CreateNewDescriptor jest wywoływana, świeży materiał klucza jest tworzony wyłącznie dla tego wywołania i tworzony jest nowy IAuthenticatedEncryptorDescriptor, który otacza ten klucz materiał i informacje algorytmowe wymagane do korzystania z materiału. Materiał klucza można utworzyć w oprogramowaniu (i przechowywać w pamięci), ale można go utworzyć i przechowywać w module HSM i tak dalej. Decydującym punktem jest to, że wszystkie dwa wywołania CreateNewDescriptor nigdy nie powinny tworzyć równoważnych wystąpień IAuthenticatedEncryptorDescriptor.

Typ AlgorithmConfiguration służy jako punkt wejścia dla procedur tworzenia kluczy, takich jak [Automatyczne wycofywanie klucza](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Aby zmienić implementację wszystkich przyszłych kluczy, ustaw właściwość AuthenticatedEncryptorConfiguration w KeyManagementOptions.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

Interfejs **IAuthenticatedEncryptorConfiguration** reprezentuje typ, który wie, jak tworzyć wystąpienia [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Udostępnia on pojedynczy interfejs API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Pomyśl o IAuthenticatedEncryptorConfiguration jako fabryki najwyższego poziomu. Konfiguracja służy jako szablon. Powoduje Zawijanie informacji o algorytmach (np. Ta konfiguracja generuje deskryptory z kluczem głównym AES-128-GCM), ale nie jest jeszcze skojarzona z określonym kluczem.

Gdy CreateNewDescriptor jest wywoływana, świeży materiał klucza jest tworzony wyłącznie dla tego wywołania i tworzony jest nowy IAuthenticatedEncryptorDescriptor, który otacza ten klucz materiał i informacje algorytmowe wymagane do korzystania z materiału. Materiał klucza można utworzyć w oprogramowaniu (i przechowywać w pamięci), ale można go utworzyć i przechowywać w module HSM i tak dalej. Decydującym punktem jest to, że wszystkie dwa wywołania CreateNewDescriptor nigdy nie powinny tworzyć równoważnych wystąpień IAuthenticatedEncryptorDescriptor.

Typ IAuthenticatedEncryptorConfiguration służy jako punkt wejścia dla procedur tworzenia kluczy, takich jak [Automatyczne wycofywanie klucza](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Aby zmienić implementację wszystkich przyszłych kluczy, zarejestruj pojedyncze IAuthenticatedEncryptorConfiguration w kontenerze usługi.

---
