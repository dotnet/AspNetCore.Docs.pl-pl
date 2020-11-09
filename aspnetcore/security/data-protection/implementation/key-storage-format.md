---
title: Format magazynu kluczy w ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji formatu magazynu kluczy ASP.NET Core ochrony danych.
ms.author: riande
ms.date: 04/08/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060147"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="4b98c-103">Format magazynu kluczy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b98c-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="4b98c-104">Obiekty są przechowywane w reprezentacji XML.</span><span class="sxs-lookup"><span data-stu-id="4b98c-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="4b98c-105">Domyślnym katalogiem magazynu kluczy jest:</span><span class="sxs-lookup"><span data-stu-id="4b98c-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="4b98c-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="4b98c-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="4b98c-107">macOS/Linux: *$Home/.ASPNET/dataprotection-Keys*</span><span class="sxs-lookup"><span data-stu-id="4b98c-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="4b98c-108">\<key>Element</span><span class="sxs-lookup"><span data-stu-id="4b98c-108">The \<key> element</span></span>

<span data-ttu-id="4b98c-109">Klucze istnieją jako obiekty najwyższego poziomu w repozytorium kluczy.</span><span class="sxs-lookup"><span data-stu-id="4b98c-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="4b98c-110">Klucze konwencji mają **klucz filename-{GUID}. XML** , gdzie {GUID} jest identyfikatorem klucza.</span><span class="sxs-lookup"><span data-stu-id="4b98c-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="4b98c-111">Każdy taki plik zawiera jeden klucz.</span><span class="sxs-lookup"><span data-stu-id="4b98c-111">Each such file contains a single key.</span></span> <span data-ttu-id="4b98c-112">Format pliku jest następujący.</span><span class="sxs-lookup"><span data-stu-id="4b98c-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="4b98c-113">\<key>Element zawiera następujące atrybuty i elementy podrzędne:</span><span class="sxs-lookup"><span data-stu-id="4b98c-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="4b98c-114">Identyfikator klucza. Ta wartość jest traktowana jako autorytatywna; Nazwa pliku jest po prostu Nicety do czytelności.</span><span class="sxs-lookup"><span data-stu-id="4b98c-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="4b98c-115">Wersja \<key> elementu, obecnie stała się 1.</span><span class="sxs-lookup"><span data-stu-id="4b98c-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="4b98c-116">Data utworzenia, aktywacji i wygaśnięcia klucza.</span><span class="sxs-lookup"><span data-stu-id="4b98c-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="4b98c-117">\<descriptor>Element, który zawiera informacje dotyczące uwierzytelnionej implementacji szyfrowania zawartej w tym kluczu.</span><span class="sxs-lookup"><span data-stu-id="4b98c-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="4b98c-118">W powyższym przykładzie identyfikator klucza to {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, został utworzony i aktywowany w 19 marca 2015 i ma okres istnienia 90 dni.</span><span class="sxs-lookup"><span data-stu-id="4b98c-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="4b98c-119">(Czasami Data aktywacji może być nieco wcześniejsza niż data utworzenia, jak w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="4b98c-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="4b98c-120">Wynika to z drobne, w jaki sposób interfejsy API działają i są nieszkodliwe w użyciu.)</span><span class="sxs-lookup"><span data-stu-id="4b98c-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="4b98c-121">\<descriptor>Element</span><span class="sxs-lookup"><span data-stu-id="4b98c-121">The \<descriptor> element</span></span>

<span data-ttu-id="4b98c-122">Element zewnętrzny \<descriptor> zawiera atrybut deserializatortype, który jest kwalifikowaną nazwą zestawu typu, który implementuje IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="4b98c-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="4b98c-123">Ten typ jest odpowiedzialny za odczytywanie \<descriptor> elementu wewnętrznego i analizowanie zawartych w nim informacji.</span><span class="sxs-lookup"><span data-stu-id="4b98c-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="4b98c-124">Określony format \<descriptor> elementu zależy od uwierzytelnionej implementacji modułu szyfrującego hermetyzowanego przez klucz, a każdy typ deserializacji oczekuje nieco innego formatu.</span><span class="sxs-lookup"><span data-stu-id="4b98c-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="4b98c-125">Ogólnie rzecz biorąc, ten element będzie zawierał informacje o algorytmach (nazwy, typy, identyfikatory OID lub podobne) i materiał klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="4b98c-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="4b98c-126">W powyższym przykładzie deskryptor określa, że ten klucz otacza algorytm AES-256-CBC szyfrowanie i HMACSHA256 weryfikację.</span><span class="sxs-lookup"><span data-stu-id="4b98c-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="4b98c-127">\<encryptedSecret>Element</span><span class="sxs-lookup"><span data-stu-id="4b98c-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="4b98c-128">Element **&lt; encryptedSecret &gt;** , który zawiera zaszyfrowaną formę materiału klucza tajnego, może być obecny, jeśli [włączono szyfrowanie wpisów tajnych w stanie spoczynku](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="4b98c-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="4b98c-129">Ten atrybut `decryptorType` to kwalifikowana dla zestawu nazwa typu, który implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="4b98c-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="4b98c-130">Ten typ jest odpowiedzialny za odczytywanie wewnętrznego elementu **&lt; encryptedKey &gt;** i odszyfrowywanie go w celu odzyskania oryginalnego tekstu.</span><span class="sxs-lookup"><span data-stu-id="4b98c-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="4b98c-131">Podobnie jak w przypadku `<descriptor>` , określony format `<encryptedSecret>` elementu zależy od mechanizmu szyfrowania w trybie Rest.</span><span class="sxs-lookup"><span data-stu-id="4b98c-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="4b98c-132">W powyższym przykładzie klucz główny jest szyfrowany przy użyciu funkcji DPAPI systemu Windows na komentarz.</span><span class="sxs-lookup"><span data-stu-id="4b98c-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="4b98c-133">\<revocation>Element</span><span class="sxs-lookup"><span data-stu-id="4b98c-133">The \<revocation> element</span></span>

<span data-ttu-id="4b98c-134">Odwołania istnieją jako obiekty najwyższego poziomu w repozytorium kluczy.</span><span class="sxs-lookup"><span data-stu-id="4b98c-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="4b98c-135">Odwołania do Konwencji mają odwołania do nazwy pliku **-{timestamp}. XML** (do odwoływania wszystkich kluczy przed określoną datą) lub **odwołania-{GUID}. XML** (na potrzeby odwoływania określonego klucza).</span><span class="sxs-lookup"><span data-stu-id="4b98c-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="4b98c-136">Każdy plik zawiera pojedynczy \<revocation> element.</span><span class="sxs-lookup"><span data-stu-id="4b98c-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="4b98c-137">W przypadku odwołań poszczególnych kluczy zawartość pliku będzie następująca.</span><span class="sxs-lookup"><span data-stu-id="4b98c-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="4b98c-138">W takim przypadku tylko określony klucz jest odwołany.</span><span class="sxs-lookup"><span data-stu-id="4b98c-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="4b98c-139">Jeśli identyfikator klucza to "\*", jednak jak w poniższym przykładzie, wszystkie klucze, których data utworzenia jest wcześniejsza niż określona data odwołania, zostaną odwołane.</span><span class="sxs-lookup"><span data-stu-id="4b98c-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="4b98c-140">\<reason>Element nigdy nie jest odczytywany przez system.</span><span class="sxs-lookup"><span data-stu-id="4b98c-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="4b98c-141">Jest to po prostu wygodne miejsce do przechowywania przyczyny odwołania przez człowieka.</span><span class="sxs-lookup"><span data-stu-id="4b98c-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
