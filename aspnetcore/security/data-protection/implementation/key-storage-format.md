---
title: Format magazynu kluczy w ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji formatu magazynu kluczy ASP.NET Core ochrony danych.
ms.author: riande
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: d284927e8ff4315b813fe36b9c335d8bd75ece11
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776867"
---
# <a name="key-storage-format-in-aspnet-core"></a>Format magazynu kluczy w ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Obiekty są przechowywane w reprezentacji XML. Domyślnym katalogiem magazynu kluczy jest:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux: *$Home/.ASPNET/dataprotection-Keys*

## <a name="the-key-element"></a>Element \<klucza>

Klucze istnieją jako obiekty najwyższego poziomu w repozytorium kluczy. Klucze konwencji mają **klucz filename-{GUID}. XML**, gdzie {GUID} jest identyfikatorem klucza. Każdy taki plik zawiera jeden klucz. Format pliku jest następujący.

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

Element \<Key> zawiera następujące atrybuty i elementy podrzędne:

* Identyfikator klucza. Ta wartość jest traktowana jako autorytatywna; Nazwa pliku jest po prostu Nicety do czytelności.

* Wersja \<kluczowego elementu>, aktualnie ustalona na 1.

* Data utworzenia, aktywacji i wygaśnięcia klucza.

* \<Deskryptor> element, który zawiera informacje dotyczące uwierzytelnionej implementacji szyfrowania zawartej w tym kluczu.

W powyższym przykładzie identyfikator klucza to {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, został utworzony i aktywowany w 19 marca 2015 i ma okres istnienia 90 dni. (Czasami Data aktywacji może być nieco wcześniejsza niż data utworzenia, jak w tym przykładzie. Wynika to z drobne, w jaki sposób interfejsy API działają i są nieszkodliwe w użyciu.)

## <a name="the-descriptor-element"></a>\<Deskryptor> elementu

Element> \<deskryptora zewnętrznego zawiera atrybut deserializatortype, który jest kwalifikowana dla zestawu nazwa typu, który implementuje IAuthenticatedEncryptorDescriptorDeserializer. Ten typ jest odpowiedzialny za odczytywanie \<deskryptora wewnętrznego> elementu oraz analizowanie zawartych w nim informacji.

Określony format elementu> \<deskryptora zależy od uwierzytelnionej implementacji modułu szyfrującego hermetyzowanego przez klucz, a każdy typ deserializacji oczekuje nieco innego formatu. Ogólnie rzecz biorąc, ten element będzie zawierał informacje o algorytmach (nazwy, typy, identyfikatory OID lub podobne) i materiał klucza tajnego. W powyższym przykładzie deskryptor określa, że ten klucz otacza algorytm AES-256-CBC szyfrowanie i HMACSHA256 weryfikację.

## <a name="the-encryptedsecret-element"></a>Element \<> encryptedSecret

Element ** &lt;encryptedSecret&gt; ** , który zawiera zaszyfrowaną formę materiału klucza tajnego, może być obecny, jeśli [włączono szyfrowanie wpisów tajnych w stanie spoczynku](xref:security/data-protection/implementation/key-encryption-at-rest). Ten atrybut `decryptorType` to kwalifikowana dla zestawu nazwa typu, który implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Ten typ jest odpowiedzialny za odczytywanie ** &lt;wewnętrznego&gt; elementu encryptedKey** i odszyfrowywanie go w celu odzyskania oryginalnego tekstu.

Podobnie jak `<descriptor>`w przypadku, określony format `<encryptedSecret>` elementu zależy od mechanizmu szyfrowania w trybie Rest. W powyższym przykładzie klucz główny jest szyfrowany przy użyciu funkcji DPAPI systemu Windows na komentarz.

## <a name="the-revocation-element"></a>Element \<> odwołania

Odwołania istnieją jako obiekty najwyższego poziomu w repozytorium kluczy. Odwołania do Konwencji mają odwołania do nazwy pliku **-{timestamp}. XML** (do odwoływania wszystkich kluczy przed określoną datą) lub **odwołania-{GUID}. XML** (na potrzeby odwoływania określonego klucza). Każdy plik zawiera pojedynczy \<element> odwołania.

W przypadku odwołań poszczególnych kluczy zawartość pliku będzie następująca.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

W takim przypadku tylko określony klucz jest odwołany. Jeśli identyfikator klucza to "*", jednak jak w poniższym przykładzie, wszystkie klucze, których data utworzenia jest wcześniejsza niż określona data odwołania, zostaną odwołane.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<Przyczyna> element nigdy nie jest odczytywany przez system. Jest to po prostu wygodne miejsce do przechowywania przyczyny odwołania przez człowieka.
