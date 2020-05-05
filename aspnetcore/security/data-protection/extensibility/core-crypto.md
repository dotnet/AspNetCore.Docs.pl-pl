---
title: Rozszerzalność kryptografii Core w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer i fabryki najwyższego poziomu.
ms.author: riande
ms.date: 08/11/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: c63cc124e1893f23c18581841194fa66848a2a1e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776425"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="263ee-103">Rozszerzalność kryptografii Core w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="263ee-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="263ee-104">Typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.</span><span class="sxs-lookup"><span data-stu-id="263ee-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="263ee-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="263ee-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="263ee-106">Interfejs **IAuthenticatedEncryptor** jest podstawowym blokiem konstrukcyjnym podsystemu kryptograficznego.</span><span class="sxs-lookup"><span data-stu-id="263ee-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="263ee-107">Istnieje zwykle jeden IAuthenticatedEncryptor na klucz, a wystąpienie IAuthenticatedEncryptor otacza cały materiał klucza kryptograficznego i informacje o algorytmach, które są niezbędne do wykonania operacji kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="263ee-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="263ee-108">Jako że nazwa sugeruje, typ jest odpowiedzialny za dostarczanie uwierzytelnianych usług szyfrowania i odszyfrowywania.</span><span class="sxs-lookup"><span data-stu-id="263ee-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="263ee-109">Udostępnia następujące dwa interfejsy API.</span><span class="sxs-lookup"><span data-stu-id="263ee-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="263ee-110">Metoda szyfrowania zwraca obiekt BLOB, który zawiera zwykły tekst ENCIPHERED oraz tag uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="263ee-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="263ee-111">Tag uwierzytelniania musi obejmować dodatkowe dane uwierzytelnione (AAD), ale w przypadku samej usługi AAD nie należy odzyskać jej od końcowego ładunku.</span><span class="sxs-lookup"><span data-stu-id="263ee-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="263ee-112">Metoda odszyfrowuje weryfikuje tag uwierzytelniania i zwraca odszyfrowany ładunek.</span><span class="sxs-lookup"><span data-stu-id="263ee-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="263ee-113">Wszystkie błędy (z wyjątkiem ArgumentNullException i podobne) powinny być dokładnie do CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="263ee-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="263ee-114">Samo wystąpienie IAuthenticatedEncryptor nie musi zawierać materiału klucza.</span><span class="sxs-lookup"><span data-stu-id="263ee-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="263ee-115">Na przykład implementacja może delegować do modułu HSM dla wszystkich operacji.</span><span class="sxs-lookup"><span data-stu-id="263ee-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="263ee-116">Jak utworzyć IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="263ee-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="263ee-117">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="263ee-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="263ee-118">Interfejs **IAuthenticatedEncryptorFactory** reprezentuje typ, który wie, jak utworzyć wystąpienie [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="263ee-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="263ee-119">Jego interfejs API jest następujący.</span><span class="sxs-lookup"><span data-stu-id="263ee-119">Its API is as follows.</span></span>

* <span data-ttu-id="263ee-120">CreateEncryptorInstance (klucz IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="263ee-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="263ee-121">W przypadku dowolnego wystąpienia IKey wszystkie uwierzytelnione, wywołane przez nią metody CreateEncryptorInstance, powinny być uważane za równoważne, jak w poniższym przykładzie kodu.</span><span class="sxs-lookup"><span data-stu-id="263ee-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="263ee-122">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="263ee-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="263ee-123">Interfejs **IAuthenticatedEncryptorDescriptor** reprezentuje typ, który wie, jak utworzyć wystąpienie [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="263ee-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="263ee-124">Jego interfejs API jest następujący.</span><span class="sxs-lookup"><span data-stu-id="263ee-124">Its API is as follows.</span></span>

* <span data-ttu-id="263ee-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="263ee-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="263ee-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="263ee-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="263ee-127">Podobnie jak w przypadku IAuthenticatedEncryptor, zakłada się, że wystąpienie IAuthenticatedEncryptorDescriptor jest zawijane według jednego określonego klucza.</span><span class="sxs-lookup"><span data-stu-id="263ee-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="263ee-128">Oznacza to, że dla każdego wystąpienia IAuthenticatedEncryptorDescriptor wszystkie uwierzytelnione szyfrujące utworzone za pomocą jego metody CreateEncryptorInstance powinny być uważane za równoważne, jak w poniższym przykładzie kodu.</span><span class="sxs-lookup"><span data-stu-id="263ee-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="263ee-129">IAuthenticatedEncryptorDescriptor (tylko ASP.NET Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="263ee-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="263ee-130">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="263ee-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="263ee-131">Interfejs **IAuthenticatedEncryptorDescriptor** reprezentuje typ, który wie, jak eksportować sam do formatu XML.</span><span class="sxs-lookup"><span data-stu-id="263ee-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="263ee-132">Jego interfejs API jest następujący.</span><span class="sxs-lookup"><span data-stu-id="263ee-132">Its API is as follows.</span></span>

* <span data-ttu-id="263ee-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="263ee-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="263ee-134">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="263ee-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="263ee-135">Serializacji XML</span><span class="sxs-lookup"><span data-stu-id="263ee-135">XML Serialization</span></span>

<span data-ttu-id="263ee-136">Podstawowa różnica między IAuthenticatedEncryptor i IAuthenticatedEncryptorDescriptor polega na tym, że deskryptor zna sposób tworzenia modułu szyfrującego i dostarczania go z prawidłowymi argumentami.</span><span class="sxs-lookup"><span data-stu-id="263ee-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="263ee-137">Rozważmy IAuthenticatedEncryptor, której implementacja opiera się na SymmetricAlgorithm i KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="263ee-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="263ee-138">Zadanie modułu szyfrującego ma używać tych typów, ale nie musi wiedzieć, gdzie pochodzą z tego typu, dlatego nie można w rzeczywistości napisać poprawnego opisu sposobu jego ponownego utworzenia, gdy aplikacja zostanie uruchomiona ponownie.</span><span class="sxs-lookup"><span data-stu-id="263ee-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="263ee-139">Deskryptor zachowuje się na wyższym poziomie.</span><span class="sxs-lookup"><span data-stu-id="263ee-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="263ee-140">Ponieważ deskryptor wie, jak utworzyć wystąpienie modułu szyfrującego (np. Informacje o sposobie tworzenia wymaganych algorytmów), można zserializować tę wiedzę w formularzu XML, tak aby wystąpienie modułu szyfrującego można odtworzyć po zresetowaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="263ee-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="263ee-141">Deskryptor może być serializowany za pośrednictwem jego procedury ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="263ee-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="263ee-142">Ta procedura zwraca element XmlSerializedDescriptorInfo, który zawiera dwie właściwości: XElement reprezentację deskryptora i typ, który reprezentuje [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) , który może być używany do przywracania aktywności tego deskryptora pod kątem odpowiedniego elementu XElement.</span><span class="sxs-lookup"><span data-stu-id="263ee-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="263ee-143">Deskryptor serializowany może zawierać informacje poufne, takie jak materiał klucza kryptograficznego.</span><span class="sxs-lookup"><span data-stu-id="263ee-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="263ee-144">System ochrony danych ma wbudowaną obsługę szyfrowania informacji przed utrwaleniem ich w magazynie.</span><span class="sxs-lookup"><span data-stu-id="263ee-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="263ee-145">Aby skorzystać z tego, deskryptor powinien oznaczać element, który zawiera poufne informacje o nazwie atrybutu "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), wartość "true".</span><span class="sxs-lookup"><span data-stu-id="263ee-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="263ee-146">Istnieje interfejs API pomocnika służący do ustawiania tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="263ee-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="263ee-147">Wywołaj metodę rozszerzenia XElement. MarkAsRequiresEncryption () znajdującą się w przestrzeni nazw Microsoft. AspNetCore. dataprotection. AuthenticatedEncryption. ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="263ee-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="263ee-148">Mogą również wystąpić przypadki, w których serializowany deskryptor nie zawiera informacji poufnych.</span><span class="sxs-lookup"><span data-stu-id="263ee-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="263ee-149">Rozważ ponowne uwzględnienie wielkości liter klucza kryptograficznego przechowywanego w module HSM.</span><span class="sxs-lookup"><span data-stu-id="263ee-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="263ee-150">Deskryptor nie może zapisywać materiału klucza podczas serializowania siebie, ponieważ moduł HSM nie ujawnia materiału w postaci zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="263ee-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="263ee-151">Zamiast tego deskryptora może napisać wersję klucza zapakowanego klucza (Jeśli moduł HSM umożliwia eksport w ten sposób) lub własny unikatowy identyfikator modułu HSM.</span><span class="sxs-lookup"><span data-stu-id="263ee-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="263ee-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="263ee-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="263ee-153">Interfejs **IAuthenticatedEncryptorDescriptorDeserializer** reprezentuje typ, który wie, jak deserializować wystąpienie IAuthenticatedEncryptorDescriptor z XElement.</span><span class="sxs-lookup"><span data-stu-id="263ee-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="263ee-154">Uwidacznia on jedną metodę:</span><span class="sxs-lookup"><span data-stu-id="263ee-154">It exposes a single method:</span></span>

* <span data-ttu-id="263ee-155">ImportFromXml (element XElement): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="263ee-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="263ee-156">Metoda ImportFromXml przyjmuje XElement, który został zwrócony przez [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) i tworzy odpowiednik oryginalnego elementu IAuthenticatedEncryptorDescriptor.</span><span class="sxs-lookup"><span data-stu-id="263ee-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="263ee-157">Typy, które implementują IAuthenticatedEncryptorDescriptorDeserializer powinny mieć jeden z następujących dwóch konstruktorów publicznych:</span><span class="sxs-lookup"><span data-stu-id="263ee-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="263ee-158">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="263ee-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="263ee-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="263ee-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="263ee-160">IServiceProvider przesłany do konstruktora może mieć wartość null.</span><span class="sxs-lookup"><span data-stu-id="263ee-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="263ee-161">Fabryka najwyższego poziomu</span><span class="sxs-lookup"><span data-stu-id="263ee-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="263ee-162">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="263ee-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="263ee-163">Klasa **AlgorithmConfiguration** reprezentuje typ, który wie, jak tworzyć wystąpienia [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="263ee-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="263ee-164">Udostępnia on pojedynczy interfejs API.</span><span class="sxs-lookup"><span data-stu-id="263ee-164">It exposes a single API.</span></span>

* <span data-ttu-id="263ee-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="263ee-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="263ee-166">Pomyśl o AlgorithmConfiguration jako fabryki najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="263ee-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="263ee-167">Konfiguracja służy jako szablon.</span><span class="sxs-lookup"><span data-stu-id="263ee-167">The configuration serves as a template.</span></span> <span data-ttu-id="263ee-168">Powoduje Zawijanie informacji o algorytmach (np. Ta konfiguracja generuje deskryptory z kluczem głównym AES-128-GCM), ale nie jest jeszcze skojarzona z określonym kluczem.</span><span class="sxs-lookup"><span data-stu-id="263ee-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="263ee-169">Gdy CreateNewDescriptor jest wywoływana, świeży materiał klucza jest tworzony wyłącznie dla tego wywołania i tworzony jest nowy IAuthenticatedEncryptorDescriptor, który otacza ten klucz materiał i informacje algorytmowe wymagane do korzystania z materiału.</span><span class="sxs-lookup"><span data-stu-id="263ee-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="263ee-170">Materiał klucza można utworzyć w oprogramowaniu (i przechowywać w pamięci), ale można go utworzyć i przechowywać w module HSM i tak dalej.</span><span class="sxs-lookup"><span data-stu-id="263ee-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="263ee-171">Decydującym punktem jest to, że wszystkie dwa wywołania CreateNewDescriptor nigdy nie powinny tworzyć równoważnych wystąpień IAuthenticatedEncryptorDescriptor.</span><span class="sxs-lookup"><span data-stu-id="263ee-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="263ee-172">Typ AlgorithmConfiguration służy jako punkt wejścia dla procedur tworzenia kluczy, takich jak [Automatyczne wycofywanie klucza](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="263ee-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="263ee-173">Aby zmienić implementację wszystkich przyszłych kluczy, ustaw właściwość AuthenticatedEncryptorConfiguration w KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="263ee-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="263ee-174">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="263ee-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="263ee-175">Interfejs **IAuthenticatedEncryptorConfiguration** reprezentuje typ, który wie, jak tworzyć wystąpienia [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="263ee-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="263ee-176">Udostępnia on pojedynczy interfejs API.</span><span class="sxs-lookup"><span data-stu-id="263ee-176">It exposes a single API.</span></span>

* <span data-ttu-id="263ee-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="263ee-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="263ee-178">Pomyśl o IAuthenticatedEncryptorConfiguration jako fabryki najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="263ee-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="263ee-179">Konfiguracja służy jako szablon.</span><span class="sxs-lookup"><span data-stu-id="263ee-179">The configuration serves as a template.</span></span> <span data-ttu-id="263ee-180">Powoduje Zawijanie informacji o algorytmach (np. Ta konfiguracja generuje deskryptory z kluczem głównym AES-128-GCM), ale nie jest jeszcze skojarzona z określonym kluczem.</span><span class="sxs-lookup"><span data-stu-id="263ee-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="263ee-181">Gdy CreateNewDescriptor jest wywoływana, świeży materiał klucza jest tworzony wyłącznie dla tego wywołania i tworzony jest nowy IAuthenticatedEncryptorDescriptor, który otacza ten klucz materiał i informacje algorytmowe wymagane do korzystania z materiału.</span><span class="sxs-lookup"><span data-stu-id="263ee-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="263ee-182">Materiał klucza można utworzyć w oprogramowaniu (i przechowywać w pamięci), ale można go utworzyć i przechowywać w module HSM i tak dalej.</span><span class="sxs-lookup"><span data-stu-id="263ee-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="263ee-183">Decydującym punktem jest to, że wszystkie dwa wywołania CreateNewDescriptor nigdy nie powinny tworzyć równoważnych wystąpień IAuthenticatedEncryptorDescriptor.</span><span class="sxs-lookup"><span data-stu-id="263ee-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="263ee-184">Typ IAuthenticatedEncryptorConfiguration służy jako punkt wejścia dla procedur tworzenia kluczy, takich jak [Automatyczne wycofywanie klucza](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="263ee-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="263ee-185">Aby zmienić implementację wszystkich przyszłych kluczy, zarejestruj pojedyncze IAuthenticatedEncryptorConfiguration w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="263ee-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
