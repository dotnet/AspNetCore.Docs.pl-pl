---
title: Szczegóły uwierzytelnionego szyfrowania w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji uwierzytelnionego szyfrowania ASP.NET Core ochrony danych.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 3066cd505781ed2ddad46626dda9d9ce35307877
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776971"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Szczegóły uwierzytelnionego szyfrowania w ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Wywołania do IDataProtector. Protect są uwierzytelnianymi operacjami szyfrowania. Metoda Protect zapewnia poufność i autentyczność oraz jest związana z łańcuchem przeznaczenia, który został użyty do wygenerowania tego konkretnego wystąpienia IDataProtector z jego głównego IDataProtectionProvideru.

IDataProtector. Protect pobiera parametr zwykłego tekstu Byte [] i tworzy ładunek zabezpieczony bajtem [], którego format został opisany poniżej. (Istnieje również Przeciążenie metody rozszerzenia, które pobiera parametr w postaci zwykłego tekstu i zwraca ładunek w postaci ciągu tekstowego. Jeśli ten interfejs API jest używany, format chronionego ładunku nadal będzie miał poniższą strukturę, ale będzie [base64url](https://tools.ietf.org/html/rfc4648#section-5).

## <a name="protected-payload-format"></a>Format chronionego ładunku

Format chronionego ładunku składa się z trzech głównych składników:

* 32-bitowy nagłówek Magic, który identyfikuje wersję systemu ochrony danych.

* Identyfikator klucza 128-bitowego, który identyfikuje klucz używany do ochrony danego ładunku.

* Pozostała część chronionego ładunku jest [specyficzna dla modułu szyfrującego hermetyzowanego przez ten klucz](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). W poniższym przykładzie klucz reprezentuje szyfrowanie AES-256-CBC + HMACSHA256, a ładunek jest dalej podzielona w następujący sposób:
  * Modyfikator klucza 128-bitowego.
  * Wektor inicjalizacji 128-bitowego.
  * 48 bajtów AES-256-CBC danych wyjściowych.
  * Tag uwierzytelniania HMACSHA256.

Przykładowy zabezpieczony ładunek jest przedstawiony poniżej.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

W formacie ładunku powyżej pierwszych 32 bitów lub 4 bajty to magiczny nagłówek identyfikujący wersję (09 F0 C9 F0)

Następny 128 bitów lub 16 bajtów jest identyfikatorem klucza (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Pozostała część zawiera ładunek i jest zależna od użytego formatu.

> [!WARNING]
> Wszystkie ładunki chronione w danym kluczu będą rozpoczynać się od tego samego, 20-bajtowego (Magiczna wartość, identyfikator klucza). Administratorzy mogą używać tego faktu w celach diagnostycznych do przybliżonego momentu wygenerowania ładunku. Na przykład powyższy ładunek odpowiada kluczowi {0c819c80-6619-4019-9536-53f8aaffee57}. Jeśli po sprawdzeniu repozytorium kluczy okaże się, że Data aktywacji określonego klucza to 2015-01-01, a jej data wygaśnięcia to 2015-03-01, rozsądne jest założenie, że ładunek (jeśli nie naruszony) został wygenerowany w tym oknie, nadaje lub przyjmuje mały współczynnik Fudge po obu stronach.
