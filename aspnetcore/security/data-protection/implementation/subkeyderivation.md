---
title: Wyprowadzanie podkluczy i uwierzytelnione szyfrowanie w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji wyprowadzenia podklucza ASP.NET Core ochrony danych i uwierzytelniania uwierzytelnionego.
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: f373c37a5ea4dab91463d011d3ecd6799ae6d014
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408035"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Wyprowadzanie podkluczy i uwierzytelnione szyfrowanie w ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

Większość kluczy w pierścieniu kluczy będzie zawierać pewną postać entropii i będzie zawierać informacje o algorytmach z uwzględnieniem "CBC-Mode Encryption + HMAC Validation" lub "GCM Encryption + Validation". W takich przypadkach odwołujemy się do osadzonej entropii jako głównego materiału klucza (lub KM) dla tego klucza i wykonujemy funkcję wyprowadzania klucza w celu uzyskania kluczy, które będą używane dla rzeczywistych operacji kryptograficznych.

> [!NOTE]
> Klucze są abstrakcyjne i implementacja niestandardowa może nie zachowywać się w następujący sposób. Jeśli klucz stanowi własną implementację `IAuthenticatedEncryptor` zamiast korzystania z jednego z naszych wbudowanych fabryk, mechanizm opisany w tej sekcji już nie ma zastosowania.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Dodatkowe uwierzytelnione dane i podklucze

`IAuthenticatedEncryptor`Interfejs służy jako podstawowy interfejs dla wszystkich uwierzytelnionych operacji szyfrowania. Jego `Encrypt` Metoda przyjmuje dwa bufory: zwykły tekst i additionalAuthenticatedData (AAD). Zawartość w postaci zwykłego tekstu nie zmieniła się z wywołaniem `IDataProtector.Protect` , ale jest generowana przez system i składa się z trzech składników:

1. 32-bitowy nagłówek Magic 09 F0 C9 F0, który identyfikuje tę wersję systemu ochrony danych.

2. Identyfikator klucza 128-bitowego.

3. Ciąg o zmiennej długości utworzony z łańcucha przeznaczenie, który utworzył tę `IDataProtector` operację.

Ponieważ usługa AAD jest unikatowa dla krotki wszystkich trzech składników, możemy użyć jej do uzyskania nowych kluczy z KM zamiast używania kilometrów we wszystkich naszych operacjach kryptograficznych. Dla każdego wywołania do programu `IAuthenticatedEncryptor.Encrypt` odbywa się następujący proces wyprowadzania klucza:

(K_E, K_H) = SP800_108_CTR_HMACSHA512 (K_M, AAD, contextHeader | | | | | | | | | | | | | |)

W tym miejscu wywołujemy instytut NIST SP800-108 KDF w trybie licznika (zobacz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), s. 5,1) z następującymi parametrami:

* Klucz wyprowadzania klucza (KDK) = K_M

* PRF = HMACSHA512

* Etykieta = additionalAuthenticatedData

* Context = contextHeader | | Modyfikator

Nagłówek kontekstu ma zmienną długość i zasadniczo służy jako odcisk palca algorytmów, dla których są wyprowadzane K_E i K_H. Modyfikator klucza jest 128-bitowym ciągiem losowo generowanym dla każdego wywołania do `Encrypt` i służy do zagwarantowania, że założenie, że funkcja KE i KH jest unikatowa dla tej operacji szyfrowania uwierzytelniania, nawet jeśli wszystkie inne dane wejściowe KDF są stałe.

W przypadku operacji sprawdzania poprawności szyfrowania w trybie CBC + HMAC, | K_E | to długość klucza szyfrowania symetrycznego bloku i | K_H | jest rozmiarem podsumowania procedury HMAC. W przypadku operacji szyfrowania GCM + walidacji, | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC-Mode Encryption + Walidacja HMAC

Po wygenerowaniu K_E za pomocą powyższego mechanizmu generujemy losowy wektor inicjalizacji i uruchamiamy algorytm szyfrowania bloku symetrycznego, aby szyfrowanie zwykły tekst. Wektor inicjalizacji i tekst szyfrowany są następnie uruchamiane za pomocą procedury HMAC, która została zainicjowana z kluczem K_H w celu utworzenia komputera MAC. Ten proces i wartość zwracana są reprezentowane graficznie poniżej.

![Proces CBC i powrót](subkeyderivation/_static/cbcprocess.png)

*Output: = modyfikator | | IV | | E_cbc (K_E, IV, dane) | | HMAC (K_H, IV | | E_cbc (K_E, IV, dane))*

> [!NOTE]
> `IDataProtector.Protect`Implementacja zwróci [nagłówek Magic i identyfikator klucza](xref:security/data-protection/implementation/authenticated-encryption-details) do danych wyjściowych przed przywróceniem go do obiektu wywołującego. Ponieważ nagłówek Magic i identyfikator klucza są niejawnie częścią usługi [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)i ponieważ modyfikator klucza jest podawany jako dane wejściowe do KDF, oznacza to, że każdy pojedynczy bajt końcowego zwróconego ładunku jest uwierzytelniany przez komputery Mac.

## <a name="galoiscounter-mode-encryption--validation"></a>Szyfrowanie w trybie Galois/licznik + Walidacja

Po wygenerowaniu K_E za pomocą powyższego mechanizmu generujemy losowo 96-bitowy identyfikator jednorazowy i uruchomimy algorytm szyfrowania bloku symetrycznego, aby szyfrowanie zwykły tekst i utworzyć tag uwierzytelniania 128-bitowy.

![Proces GCM i powrót](subkeyderivation/_static/galoisprocess.png)

*Output: = modyfikator | | Identyfikator jednorazowy | | E_gcm (K_E, nonce, dane) | | authTag*

> [!NOTE]
> Mimo że GCM natywnie obsługuje koncepcję usługi AAD, nadal będziemy uzyskiwać dostęp do usługi AAD tylko do oryginalnego KDF, ale w celu przekazania pustego ciągu do GCM dla jego parametru AAD. Przyczyną tego jest złożenie dwóch. Najpierw, [Aby obsługiwać elastyczność](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) , która nigdy nie chce używać K_M bezpośrednio jako klucz szyfrowania. Ponadto GCM nakładają na dane wejściowe bardzo rygorystyczne wymagania unikatowości. Prawdopodobieństwo wywołania procedury szyfrowania GCM w dwóch lub większej liczbie odrębnych zestawów danych wejściowych o tej samej parze (Key, nonce) nie może przekroczyć 2 ^ 32. Jeśli naprawimy K_E nie możemy wykonać więcej niż 2 ^ 32 operacji szyfrowania przed uruchomieniem afoul limitu 2 ^-32. Może to wyglądać podobnie do bardzo dużej liczby operacji, ale serwer sieci Web o wysokim natężeniu ruchu może przechodzić przez 4 000 000 000 żądań w zwykłych dniach, a także w normalnym okresie istnienia tych kluczy. Aby zachować zgodność z 2-32 ograniczeniem prawdopodobieństwa, będziemy nadal używać modyfikatora klucza 128-bitowego i 96-bitowy identyfikator jednorazowy, który radykalnie rozszerza liczbę operacji przydatnych dla danego K_M. W celu uproszczenia projektowania udostępnimy ścieżkę kodu KDF między operacjami CBC i GCM, a ponieważ usługa AAD jest już brana pod uwagę w KDF, nie ma potrzeby przechodzenia do procedury GCM.
