---
title: Format pamięci masowej kluczy w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji formatu magazynu kluczy ASP.NET ochrony danych.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976940"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="53574-103">Format pamięci masowej kluczy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53574-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="53574-104">Obiekty są przechowywane w spoczynku w reprezentacji XML.</span><span class="sxs-lookup"><span data-stu-id="53574-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="53574-105">Domyślnym katalogiem magazynu kluczy jest:</span><span class="sxs-lookup"><span data-stu-id="53574-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="53574-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="53574-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="53574-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="53574-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="53574-108">Kluczowym \<elementem></span><span class="sxs-lookup"><span data-stu-id="53574-108">The \<key> element</span></span>

<span data-ttu-id="53574-109">Klucze istnieją jako obiekty najwyższego poziomu w repozytorium kluczy.</span><span class="sxs-lookup"><span data-stu-id="53574-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="53574-110">Według konwencji klucze mają **klucz-{guid}.xml**, gdzie {guid} jest identyfikatorem klucza.</span><span class="sxs-lookup"><span data-stu-id="53574-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="53574-111">Każdy taki plik zawiera jeden klucz.</span><span class="sxs-lookup"><span data-stu-id="53574-111">Each such file contains a single key.</span></span> <span data-ttu-id="53574-112">Format pliku jest następujący.</span><span class="sxs-lookup"><span data-stu-id="53574-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="53574-113">Element \<> klucza zawiera następujące atrybuty i elementy podrzędne:</span><span class="sxs-lookup"><span data-stu-id="53574-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="53574-114">Identyfikator klucza. Ta wartość jest traktowana jako miarodajna; nazwa pliku jest po prostu miły dla ludzkiej czytelności.</span><span class="sxs-lookup"><span data-stu-id="53574-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="53574-115">Wersja \<elementu> klucza, obecnie ustalona na 1.</span><span class="sxs-lookup"><span data-stu-id="53574-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="53574-116">Daty utworzenia, aktywacji i wygaśnięcia klucza.</span><span class="sxs-lookup"><span data-stu-id="53574-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="53574-117">Deskryptor \<> element, który zawiera informacje o implementacji uwierzytelnionego szyfrowania zawarte w tym kluczu.</span><span class="sxs-lookup"><span data-stu-id="53574-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="53574-118">W powyższym przykładzie identyfikator klucza to {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, został utworzony i aktywowany 19 marca 2015 r. i ma okres istnienia 90 dni.</span><span class="sxs-lookup"><span data-stu-id="53574-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="53574-119">(Od czasu do czasu data aktywacji może być nieco przed datą utworzenia, jak w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="53574-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="53574-120">Wynika to z nit w jaki sposób interfejsy API pracy i jest nieszkodliwe w praktyce.)</span><span class="sxs-lookup"><span data-stu-id="53574-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="53574-121">Deskryptor \<> element</span><span class="sxs-lookup"><span data-stu-id="53574-121">The \<descriptor> element</span></span>

<span data-ttu-id="53574-122">Zewnętrzny \<deskryptor> element zawiera atrybut deserializerType, który jest nazwą kwalifikowaną do zestawu typu, który implementuje IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="53574-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="53574-123">Ten typ jest odpowiedzialny \<za odczytanie wewnętrznego deskryptora> elementu i analizowanie informacji zawartych w.</span><span class="sxs-lookup"><span data-stu-id="53574-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="53574-124">Określony format \<deskryptora> element zależy od implementacji uwierzytelnionego szyfratora zhermetyzowany przez klucz, a każdy typ deserializator oczekuje nieco inny format dla tego.</span><span class="sxs-lookup"><span data-stu-id="53574-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="53574-125">Ogólnie rzecz biorąc jednak ten element będzie zawierać informacje algorytmiczne (nazwy, typy, identyfikatory AMI lub podobne) i tajny materiał klucza.</span><span class="sxs-lookup"><span data-stu-id="53574-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="53574-126">W powyższym przykładzie deskryptor określa, że ten klucz otacza szyfrowanie AES-256-CBC + sprawdzanie poprawności HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="53574-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="53574-127">Element \<> encryptedSecret</span><span class="sxs-lookup"><span data-stu-id="53574-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="53574-128">\*\* &lt;Elementów encryptedSecret,&gt; \*\* który zawiera zaszyfrowaną formę tajnego materiału klucza może być obecny, jeśli [szyfrowanie wpisów tajnych w spoczynku jest włączone](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="53574-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="53574-129">Atrybut `decryptorType` jest nazwą kwalifikowaną do zestawu typu implementującego [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="53574-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="53574-130">Ten typ jest odpowiedzialny za odczytanie wewnętrznego \*\* &lt;elementu encryptedKey&gt; \*\* i odszyfrowywanie go w celu odzyskania oryginalnego zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="53574-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="53574-131">Podobnie `<descriptor>`jak w przypadku `<encryptedSecret>` , określony format elementu zależy od używanego mechanizmu szyfrowania w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="53574-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="53574-132">W powyższym przykładzie klucz główny jest szyfrowany przy użyciu interfejsu DPAPI systemu Windows na komentarz.</span><span class="sxs-lookup"><span data-stu-id="53574-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="53574-133">Element \<> odwołania</span><span class="sxs-lookup"><span data-stu-id="53574-133">The \<revocation> element</span></span>

<span data-ttu-id="53574-134">Odwołania istnieją jako obiekty najwyższego poziomu w repozytorium kluczy.</span><span class="sxs-lookup"><span data-stu-id="53574-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="53574-135">Zgodnie z konwencją odwołania mają nazwę pliku **revocation-{timestamp}.xml** (do odwoływania wszystkich kluczy przed określoną datą) lub **odwołania-{guid}.xml** (do odwoływania określonego klucza).</span><span class="sxs-lookup"><span data-stu-id="53574-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="53574-136">Każdy plik zawiera \<pojedynczy element odwołania>.</span><span class="sxs-lookup"><span data-stu-id="53574-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="53574-137">W przypadku odwołań poszczególnych kluczy zawartość pliku będzie następująca poniżej.</span><span class="sxs-lookup"><span data-stu-id="53574-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="53574-138">W takim przypadku odwołany jest tylko określony klucz.</span><span class="sxs-lookup"><span data-stu-id="53574-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="53574-139">Jeśli identyfikator klucza to "\*", jednak, jak w poniższym przykładzie, wszystkie klucze, których data utworzenia jest przed określoną datą odwołania, są odwołane.</span><span class="sxs-lookup"><span data-stu-id="53574-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="53574-140">Powodem \<> element nigdy nie jest odczytywany przez system.</span><span class="sxs-lookup"><span data-stu-id="53574-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="53574-141">Jest to po prostu wygodne miejsce do przechowywania czytelnego dla człowieka powodu odwołania.</span><span class="sxs-lookup"><span data-stu-id="53574-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
