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
# <a name="key-storage-format-in-aspnet-core"></a>Format pamięci masowej kluczy w ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Obiekty są przechowywane w spoczynku w reprezentacji XML. Domyślnym katalogiem magazynu kluczy jest:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>Kluczowym \<elementem>

Klucze istnieją jako obiekty najwyższego poziomu w repozytorium kluczy. Według konwencji klucze mają **klucz-{guid}.xml**, gdzie {guid} jest identyfikatorem klucza. Każdy taki plik zawiera jeden klucz. Format pliku jest następujący.

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

Element \<> klucza zawiera następujące atrybuty i elementy podrzędne:

* Identyfikator klucza. Ta wartość jest traktowana jako miarodajna; nazwa pliku jest po prostu miły dla ludzkiej czytelności.

* Wersja \<elementu> klucza, obecnie ustalona na 1.

* Daty utworzenia, aktywacji i wygaśnięcia klucza.

* Deskryptor \<> element, który zawiera informacje o implementacji uwierzytelnionego szyfrowania zawarte w tym kluczu.

W powyższym przykładzie identyfikator klucza to {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, został utworzony i aktywowany 19 marca 2015 r. i ma okres istnienia 90 dni. (Od czasu do czasu data aktywacji może być nieco przed datą utworzenia, jak w tym przykładzie. Wynika to z nit w jaki sposób interfejsy API pracy i jest nieszkodliwe w praktyce.)

## <a name="the-descriptor-element"></a>Deskryptor \<> element

Zewnętrzny \<deskryptor> element zawiera atrybut deserializerType, który jest nazwą kwalifikowaną do zestawu typu, który implementuje IAuthenticatedEncryptorDescriptorDeserializer. Ten typ jest odpowiedzialny \<za odczytanie wewnętrznego deskryptora> elementu i analizowanie informacji zawartych w.

Określony format \<deskryptora> element zależy od implementacji uwierzytelnionego szyfratora zhermetyzowany przez klucz, a każdy typ deserializator oczekuje nieco inny format dla tego. Ogólnie rzecz biorąc jednak ten element będzie zawierać informacje algorytmiczne (nazwy, typy, identyfikatory AMI lub podobne) i tajny materiał klucza. W powyższym przykładzie deskryptor określa, że ten klucz otacza szyfrowanie AES-256-CBC + sprawdzanie poprawności HMACSHA256.

## <a name="the-encryptedsecret-element"></a>Element \<> encryptedSecret

** &lt;Elementów encryptedSecret,&gt; ** który zawiera zaszyfrowaną formę tajnego materiału klucza może być obecny, jeśli [szyfrowanie wpisów tajnych w spoczynku jest włączone](xref:security/data-protection/implementation/key-encryption-at-rest). Atrybut `decryptorType` jest nazwą kwalifikowaną do zestawu typu implementującego [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Ten typ jest odpowiedzialny za odczytanie wewnętrznego ** &lt;elementu encryptedKey&gt; ** i odszyfrowywanie go w celu odzyskania oryginalnego zwykłego tekstu.

Podobnie `<descriptor>`jak w przypadku `<encryptedSecret>` , określony format elementu zależy od używanego mechanizmu szyfrowania w spoczynku. W powyższym przykładzie klucz główny jest szyfrowany przy użyciu interfejsu DPAPI systemu Windows na komentarz.

## <a name="the-revocation-element"></a>Element \<> odwołania

Odwołania istnieją jako obiekty najwyższego poziomu w repozytorium kluczy. Zgodnie z konwencją odwołania mają nazwę pliku **revocation-{timestamp}.xml** (do odwoływania wszystkich kluczy przed określoną datą) lub **odwołania-{guid}.xml** (do odwoływania określonego klucza). Każdy plik zawiera \<pojedynczy element odwołania>.

W przypadku odwołań poszczególnych kluczy zawartość pliku będzie następująca poniżej.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

W takim przypadku odwołany jest tylko określony klucz. Jeśli identyfikator klucza to "*", jednak, jak w poniższym przykładzie, wszystkie klucze, których data utworzenia jest przed określoną datą odwołania, są odwołane.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Powodem \<> element nigdy nie jest odczytywany przez system. Jest to po prostu wygodne miejsce do przechowywania czytelnego dla człowieka powodu odwołania.
