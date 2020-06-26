---
title: Nagłówki kontekstu w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji nagłówków kontekstu ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 078392662281253b8b6cfc0d50fddc8d66482b63
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406897"
---
# <a name="context-headers-in-aspnet-core"></a>Nagłówki kontekstu w ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Tło i teorii

W systemie ochrony danych "klucz" oznacza obiekt, który może zapewniać uwierzytelnione usługi szyfrowania. Każdy klucz jest identyfikowany za pomocą unikatowego identyfikatora (GUID) i zawiera informacje o algorytmach IT i materiał entropic. Należy zastanowić się, że każdy klucz ma unikatową entropię, ale system nie może wymusić tego działania, a ponadto musimy uwzględnić deweloperów, którzy mogą ręcznie zmienić pierścień kluczy, modyfikując informacje o algorytmach istniejącego klucza w pęku kluczy. W celu osiągnięcia naszych wymagań w zakresie bezpieczeństwa w tym przypadku system ochrony danych ma koncepcję [kryptografii kryptograficznej](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), która umożliwia bezpieczne używanie jednej wartości entropic w wielu algorytmach kryptograficznych.

Większość systemów, które obsługują elastyczność kryptograficzną, to przez uwzględnienie pewnych informacji identyfikujących algorytm wewnątrz ładunku. Identyfikator OID algorytmu jest zwykle dobrym kandydatem do tego. Jednak jeden z nich wystąpił, że istnieje wiele sposobów na określenie tego samego algorytmu: "AES" (CNG), a zarządzane algorytmy AES, AesManaged, AesCryptoServiceProvider, AesCng i RijndaelManaged (dane o określonych parametrach) są faktycznie tymi samymi, a musimy zachować mapowanie wszystkich tych elementów do właściwego identyfikatora OID. Jeśli deweloper chciał udostępnić niestandardowy algorytm (lub nawet inną implementację algorytmu AES), musi powiedzieć nas o jego identyfikatorze OID. Ten dodatkowy krok rejestracji powoduje, że konfiguracja systemu jest szczególnie bolesnym.

Przechodzenie przez nas z powrotem zadecydował się, że wystąpił problem z niewłaściwym kierunkiem. Identyfikator OID informuje o tym, czym jest algorytm, ale nie jest to naprawdę ważne. Jeśli musimy bezpiecznie użyć pojedynczej wartości entropic w dwóch różnych algorytmach, nie jest to konieczne, aby wiedzieć, jakie algorytmy rzeczywiście są. Zadbajmy o to, jak działa. Każdy algorytm szyfrowania bloku symetrycznego znośnego jest również silnie pseudolosowych permutacji (PRP): Popraw dane wejściowe (klucz, tryb łańcucha, IV, zwykły tekst) i dane wyjściowe tekstu szyfrowanego. Podobnie każda znośnegoa funkcja skrótu jest również silną funkcją pseudolosowych (PRF) i ze stałym zestawem danych wejściowych, która będzie zależeć od dowolnej innej funkcji skrótu z ustawioną instrukcją.

Używamy tej koncepcji mocnych PRPs i PRFs do kompilowania nagłówka kontekstu. Ten nagłówek kontekstu zasadniczo działa jako stabilny odcisk palca dla algorytmów używanych dla danej operacji i zapewnia elastyczność kryptograficzną wymaganą przez system ochrony danych. Ten nagłówek jest powtarzalny i używany później jako część [procesu wyprowadzania podklucza](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Istnieją dwa różne sposoby tworzenia nagłówka kontekstu w zależności od trybów działania podstawowych algorytmów.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Szyfrowanie w trybie CBC + uwierzytelnianie HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Nagłówek kontekstu składa się z następujących składników:

* [16 bitów] Wartość 00 00, która jest znacznikiem znaczenia "CBC Encryption + HMAC Authentication".

* [32 bitów] Długość klucza (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.

* [32 bitów] Rozmiar bloku (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.

* [32 bitów] Długość klucza (w bajtach, big-endian) algorytmu HMAC. (Obecnie rozmiar klucza zawsze jest zgodny z rozmiarem podsumowania).

* [32 bitów] Rozmiar podsumowania (w bajtach, big-endian) algorytmu HMAC.

* EncCBC (K_E, IV, ""), który jest wyjściem algorytmu szyfrowania symetrycznego, podano puste dane wejściowe i gdzie IV jest wektorem "All-zero". Konstrukcja K_E została opisana poniżej.

* MAC (K_H, ""), który jest wyjściem algorytmu HMAC, z uwzględnieniem pustego ciągu wejściowego. Konstrukcja K_H została opisana poniżej.

Najlepszym rozwiązaniem jest przekazanie wszystkich wektorów o wartości zero dla K_E i K_H. Jednak chcemy unikać sytuacji, w której podstawowy algorytm sprawdza obecność słabych kluczy przed wykonaniem jakiejkolwiek operacji (w szczególności DES i 3DES), która wyklucza użycie prostego lub powtarzalnego wzorca, takiego jak wektor "All-zero".

Zamiast tego używamy NIST SP800-108 KDF w trybie licznika (zobacz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), sek. 5,1) z kluczem o zerowej długości, etykiecie i kontekście oraz HMACSHA512 jako bazowego PRF. Firma Microsoft dziedziczy | K_E | + | K_H | bajty danych wyjściowych, a następnie Rozłóż wynik do K_E i K_H siebie. Matematycznie jest to reprezentowane w następujący sposób.

(K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Przykład: AES-192-CBC + HMACSHA256

Przykładowo Rozważmy przypadek, w którym algorytm szyfrowania bloku symetrycznego to AES-192-CBC, a algorytm walidacji to HMACSHA256. System wygeneruje nagłówek kontekstu, wykonując poniższe kroki.

Najpierw Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = ""), gdzie | K_E | = 192 bitów i | K_H | = 256 bitów według określonych algorytmów. To prowadzi do K_E = 5BB6.. 21DD i K_H = A04A.. 00A9 w poniższym przykładzie:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Następnie Enc_CBC obliczeniowe (K_E, IV, "") dla algorytmu AES-192-CBC przyznany IV = 0 * i K_E jak powyżej.

wynik: = F474B1872B3B53E4721DE19C0841DB6F

Następnie należy obliczyć komputery MAC (K_H "") dla HMACSHA256 K_H jak powyżej.

wynik: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Spowoduje to utworzenie pełnego nagłówka kontekstu poniżej:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Ten nagłówek kontekstu jest odciskiem palca uwierzytelnionej pary algorytmu szyfrowania (AES-192-CBC Encryption + HMACSHA256 Validation). Składniki, zgodnie z [powyższym](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) opisem, to:

* znacznik (00 00)

* Długość klucza szyfrowania bloku (00 00 00 18)

* rozmiar bloku szyfrowania bloku (00 00 00 10)

* Długość klucza HMAC (00 00 00 20)

* rozmiar podsumowania HMAC (00 00 00 20)

* blok danych wyjściowych szyfrowania z szyfrowaniem (F4 74-DB 6F) i

* PRF dane wyjściowe HMAC (D4 79-end).

> [!NOTE]
> Nagłówek kontekstu uwierzytelniania CBC + HMAC jest zbudowany w taki sam sposób, niezależnie od tego, czy implementacje algorytmów są dostarczane przez system Windows CNG, czy przez zarządzane typy SymmetricAlgorithm i KeyedHashAlgorithm. Pozwala to aplikacjom działającym w różnych systemach operacyjnych na niezawodne generowanie tego samego nagłówka kontekstu, mimo że implementacje algorytmów różnią się między systemów operacyjnych. (W przypadku KeyedHashAlgorithm nie musi być prawidłowym elementem HMAC. Może to być dowolny typ algorytmu wyznaczania wartości skrótu.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Przykład: 3DES-192-CBC + HMACSHA1

Najpierw Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = ""), gdzie | K_E | = 192 bitów i | K_H | = 160 bitów według określonych algorytmów. To prowadzi do K_E = A219.. E2BB i K_H = DC4A.. B464 w poniższym przykładzie:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Następnie Enc_CBC obliczeniowe (K_E, IV, "") dla algorytmu 3DES-192-CBC przyznany IV = 0 * i K_E jak powyżej.

wynik: = ABB100F81E53E10E

Następnie należy obliczyć komputery MAC (K_H "") dla HMACSHA1 K_H jak powyżej.

wynik: = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Spowoduje to utworzenie pełnego nagłówka kontekstu, który jest odciskiem palca pary szyfrowany algorytm szyfrowania (3DES-192-CBC Encryption + HMACSHA1 Validation), jak pokazano poniżej:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Składniki są podzielone w następujący sposób:

* znacznik (00 00)

* Długość klucza szyfrowania bloku (00 00 00 18)

* rozmiar bloku szyfrowania bloku (00 00 00 08)

* Długość klucza HMAC (00 00 00 14)

* rozmiar podsumowania HMAC (00 00 00 14)

* blok danych wyjściowych szyfrowania z szyfrowaniem (AB B1-E1 0E) i

* PRF dane wyjściowe HMAC (76 EB-end).

## <a name="galoiscounter-mode-encryption--authentication"></a>Szyfrowanie i tryb Galois/uwierzytelnianie

Nagłówek kontekstu składa się z następujących składników:

* [16 bitów] Wartość 00 01, która jest znacznikiem znaczenia "GCM Encryption + Authentication".

* [32 bitów] Długość klucza (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.

* [32 bitów] Rozmiar jednorazowy (w bajtach, big-endian) używany podczas operacji uwierzytelniania uwierzytelnionego. (W przypadku systemu ten problem jest ustalony w rozmiarze nonce = 96 bitów).

* [32 bitów] Rozmiar bloku (w bajtach, big-endian) algorytmu szyfrowania symetrycznego. (W przypadku GCM jest to ustalone w rozmiarze bloku = 128 bitów).

* [32 bitów] Rozmiar znacznika uwierzytelniania (w bajtach, big-endian) generowany przez uwierzytelnioną funkcję szyfrowania. (W przypadku systemu ten problem jest ustalany w rozmiarze znacznika = 128 bitów).

* [128 bitów] Tag Enc_GCM (K_E, nonce, ""), który jest wyjściem algorytmu szyfrowania bloku symetrycznego, z uwzględnieniem pustego ciągu wejściowego i gdzie nonce to 96-bit All-zero wektor.

K_E jest uzyskiwany przy użyciu tego samego mechanizmu co w scenariuszu uwierzytelniania CBC Encryption + HMAC. Ponieważ jednak w tym miejscu nie ma K_H, firma Microsoft ma głównie | K_H | = 0, a algorytm jest zwijany do poniższego formularza.

K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = "")

### <a name="example-aes-256-gcm"></a>Przykład: AES-256-GCM

Najpierw Zezwól na K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = ""), gdzie | K_E | = 256 bitów.

K_E: = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Następnie Oblicz tag uwierzytelniania Enc_GCM (K_E, nonce, "") dla algorytmu AES-256-GCM danego identyfikatora nonce = 096 i K_E jak powyżej.

wynik: = E7DCCE66DF855A323A6BB7BD7A59BE45

Spowoduje to utworzenie pełnego nagłówka kontekstu poniżej:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Składniki są podzielone w następujący sposób:

* znacznik (00 01)

* Długość klucza szyfrowania bloku (00 00 00 20)

* rozmiar jednorazowy (00 00 00 0C)

* rozmiar bloku szyfrowania bloku (00 00 00 10)

* rozmiar znacznika uwierzytelniania (00 00 00 10) i

* Tag uwierzytelniania z uruchamiania szyfru blokowego (E7 DC-end).
