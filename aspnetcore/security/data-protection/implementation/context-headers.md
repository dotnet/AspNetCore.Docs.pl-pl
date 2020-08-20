---
title: Nagłówki kontekstu w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji nagłówków kontekstu ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2f07db4b7d8bca9f64aee5d60e88fc92dc8965eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633711"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="a70a7-103">Nagłówki kontekstu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a70a7-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="a70a7-104">Tło i teorii</span><span class="sxs-lookup"><span data-stu-id="a70a7-104">Background and theory</span></span>

<span data-ttu-id="a70a7-105">W systemie ochrony danych "klucz" oznacza obiekt, który może zapewniać uwierzytelnione usługi szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="a70a7-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="a70a7-106">Każdy klucz jest identyfikowany za pomocą unikatowego identyfikatora (GUID) i zawiera informacje o algorytmach IT i materiał entropic.</span><span class="sxs-lookup"><span data-stu-id="a70a7-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="a70a7-107">Należy zastanowić się, że każdy klucz ma unikatową entropię, ale system nie może wymusić tego działania, a ponadto musimy uwzględnić deweloperów, którzy mogą ręcznie zmienić pierścień kluczy, modyfikując informacje o algorytmach istniejącego klucza w pęku kluczy.</span><span class="sxs-lookup"><span data-stu-id="a70a7-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="a70a7-108">W celu osiągnięcia naszych wymagań w zakresie bezpieczeństwa w tym przypadku system ochrony danych ma koncepcję [kryptografii kryptograficznej](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), która umożliwia bezpieczne używanie jednej wartości entropic w wielu algorytmach kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="a70a7-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="a70a7-109">Większość systemów, które obsługują elastyczność kryptograficzną, to przez uwzględnienie pewnych informacji identyfikujących algorytm wewnątrz ładunku.</span><span class="sxs-lookup"><span data-stu-id="a70a7-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="a70a7-110">Identyfikator OID algorytmu jest zwykle dobrym kandydatem do tego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="a70a7-111">Jednak jeden z nich wystąpił, że istnieje wiele sposobów na określenie tego samego algorytmu: "AES" (CNG), a zarządzane algorytmy AES, AesManaged, AesCryptoServiceProvider, AesCng i RijndaelManaged (dane o określonych parametrach) są faktycznie tymi samymi, a musimy zachować mapowanie wszystkich tych elementów do właściwego identyfikatora OID.</span><span class="sxs-lookup"><span data-stu-id="a70a7-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="a70a7-112">Jeśli deweloper chciał udostępnić niestandardowy algorytm (lub nawet inną implementację algorytmu AES), musi powiedzieć nas o jego identyfikatorze OID.</span><span class="sxs-lookup"><span data-stu-id="a70a7-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="a70a7-113">Ten dodatkowy krok rejestracji powoduje, że konfiguracja systemu jest szczególnie bolesnym.</span><span class="sxs-lookup"><span data-stu-id="a70a7-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="a70a7-114">Przechodzenie przez nas z powrotem zadecydował się, że wystąpił problem z niewłaściwym kierunkiem.</span><span class="sxs-lookup"><span data-stu-id="a70a7-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="a70a7-115">Identyfikator OID informuje o tym, czym jest algorytm, ale nie jest to naprawdę ważne.</span><span class="sxs-lookup"><span data-stu-id="a70a7-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="a70a7-116">Jeśli musimy bezpiecznie użyć pojedynczej wartości entropic w dwóch różnych algorytmach, nie jest to konieczne, aby wiedzieć, jakie algorytmy rzeczywiście są.</span><span class="sxs-lookup"><span data-stu-id="a70a7-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="a70a7-117">Zadbajmy o to, jak działa.</span><span class="sxs-lookup"><span data-stu-id="a70a7-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="a70a7-118">Każdy algorytm szyfrowania bloku symetrycznego znośnego jest również silnie pseudolosowych permutacji (PRP): Popraw dane wejściowe (klucz, tryb łańcucha, IV, zwykły tekst) i dane wyjściowe tekstu szyfrowanego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="a70a7-119">Podobnie każda znośnegoa funkcja skrótu jest również silną funkcją pseudolosowych (PRF) i ze stałym zestawem danych wejściowych, która będzie zależeć od dowolnej innej funkcji skrótu z ustawioną instrukcją.</span><span class="sxs-lookup"><span data-stu-id="a70a7-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="a70a7-120">Używamy tej koncepcji mocnych PRPs i PRFs do kompilowania nagłówka kontekstu.</span><span class="sxs-lookup"><span data-stu-id="a70a7-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="a70a7-121">Ten nagłówek kontekstu zasadniczo działa jako stabilny odcisk palca dla algorytmów używanych dla danej operacji i zapewnia elastyczność kryptograficzną wymaganą przez system ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="a70a7-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="a70a7-122">Ten nagłówek jest powtarzalny i używany później jako część [procesu wyprowadzania podklucza](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="a70a7-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="a70a7-123">Istnieją dwa różne sposoby tworzenia nagłówka kontekstu w zależności od trybów działania podstawowych algorytmów.</span><span class="sxs-lookup"><span data-stu-id="a70a7-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="a70a7-124">Szyfrowanie w trybie CBC + uwierzytelnianie HMAC</span><span class="sxs-lookup"><span data-stu-id="a70a7-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="a70a7-125">Nagłówek kontekstu składa się z następujących składników:</span><span class="sxs-lookup"><span data-stu-id="a70a7-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="a70a7-126">[16 bitów] Wartość 00 00, która jest znacznikiem znaczenia "CBC Encryption + HMAC Authentication".</span><span class="sxs-lookup"><span data-stu-id="a70a7-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="a70a7-127">[32 bitów] Długość klucza (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="a70a7-128">[32 bitów] Rozmiar bloku (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="a70a7-129">[32 bitów] Długość klucza (w bajtach, big-endian) algorytmu HMAC.</span><span class="sxs-lookup"><span data-stu-id="a70a7-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="a70a7-130">(Obecnie rozmiar klucza zawsze jest zgodny z rozmiarem podsumowania).</span><span class="sxs-lookup"><span data-stu-id="a70a7-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="a70a7-131">[32 bitów] Rozmiar podsumowania (w bajtach, big-endian) algorytmu HMAC.</span><span class="sxs-lookup"><span data-stu-id="a70a7-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="a70a7-132">`EncCBC(K_E, IV, "")`, który jest wyjściem algorytmu szyfrowania symetrycznego, z uwzględnieniem pustego ciągu wejściowego i gdzie IV jest wektorem "All-zero".</span><span class="sxs-lookup"><span data-stu-id="a70a7-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="a70a7-133">Konstrukcja `K_E` jest opisana poniżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="a70a7-134">`MAC(K_H, "")`, który jest wyjściem algorytmu HMAC, z uwzględnieniem pustego wejścia ciągu.</span><span class="sxs-lookup"><span data-stu-id="a70a7-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="a70a7-135">Konstrukcja `K_H` jest opisana poniżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="a70a7-136">Najlepszym rozwiązaniem jest przekazanie wszystkich wektorów o wartości zero dla `K_E` i `K_H` .</span><span class="sxs-lookup"><span data-stu-id="a70a7-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="a70a7-137">Jednak chcemy unikać sytuacji, w której podstawowy algorytm sprawdza obecność słabych kluczy przed wykonaniem jakiejkolwiek operacji (w szczególności DES i 3DES), która wyklucza użycie prostego lub powtarzalnego wzorca, takiego jak wektor "All-zero".</span><span class="sxs-lookup"><span data-stu-id="a70a7-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="a70a7-138">Zamiast tego używamy NIST SP800-108 KDF w trybie licznika (zobacz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), sek. 5,1) z kluczem o zerowej długości, etykiecie i kontekście oraz HMACSHA512 jako bazowego PRF.</span><span class="sxs-lookup"><span data-stu-id="a70a7-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="a70a7-139">Wykorzystamy `| K_E | + | K_H |` bajty danych wyjściowych, a następnie dzielą wynik na `K_E` `K_H` siebie i.</span><span class="sxs-lookup"><span data-stu-id="a70a7-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="a70a7-140">Matematycznie jest to reprezentowane w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="a70a7-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="a70a7-141">Przykład: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="a70a7-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="a70a7-142">Przykładowo Rozważmy przypadek, w którym algorytm szyfrowania bloku symetrycznego to AES-192-CBC, a algorytm walidacji to HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="a70a7-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="a70a7-143">System wygeneruje nagłówek kontekstu, wykonując poniższe kroki.</span><span class="sxs-lookup"><span data-stu-id="a70a7-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="a70a7-144">Po pierwsze, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , gdzie `| K_E | = 192 bits` i `| K_H | = 256 bits` według określonych algorytmów.</span><span class="sxs-lookup"><span data-stu-id="a70a7-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="a70a7-145">Ten lider prowadzi do `K_E = 5BB6..21DD` i `K_H = A04A..00A9` w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a70a7-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="a70a7-146">Następnie należy obliczyć `Enc_CBC (K_E, IV, "")` dane dotyczące algorytmu AES-192-CBC `IV = 0*` i `K_E` jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="a70a7-147">Następnie Oblicz `MAC(K_H, "")` dla HMACSHA256 podaną `K_H` powyżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="a70a7-148">Spowoduje to utworzenie pełnego nagłówka kontekstu poniżej:</span><span class="sxs-lookup"><span data-stu-id="a70a7-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="a70a7-149">Ten nagłówek kontekstu jest odciskiem palca uwierzytelnionej pary algorytmu szyfrowania (AES-192-CBC Encryption + HMACSHA256 Validation).</span><span class="sxs-lookup"><span data-stu-id="a70a7-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="a70a7-150">Składniki, zgodnie z [powyższym](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) opisem, to:</span><span class="sxs-lookup"><span data-stu-id="a70a7-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="a70a7-151">znacznik `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="a70a7-152">Długość klucza szyfrowania bloku `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="a70a7-153">rozmiar bloku szyfrowania bloku `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="a70a7-154">Długość klucza HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="a70a7-155">rozmiar podsumowania HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="a70a7-156">blokowe szyfrowanie danych wyjściowych szyfrowania `(F4 74 - DB 6F)` i</span><span class="sxs-lookup"><span data-stu-id="a70a7-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="a70a7-157">PRF dane wyjściowe HMAC `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="a70a7-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="a70a7-158">Nagłówek kontekstu uwierzytelniania CBC + HMAC jest zbudowany w taki sam sposób, niezależnie od tego, czy implementacje algorytmów są dostarczane przez system Windows CNG, czy przez zarządzane typy SymmetricAlgorithm i KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="a70a7-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="a70a7-159">Pozwala to aplikacjom działającym w różnych systemach operacyjnych na niezawodne generowanie tego samego nagłówka kontekstu, mimo że implementacje algorytmów różnią się między systemów operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="a70a7-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="a70a7-160">(W przypadku KeyedHashAlgorithm nie musi być prawidłowym elementem HMAC.</span><span class="sxs-lookup"><span data-stu-id="a70a7-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="a70a7-161">Może to być dowolny typ algorytmu wyznaczania wartości skrótu.)</span><span class="sxs-lookup"><span data-stu-id="a70a7-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="a70a7-162">Przykład: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="a70a7-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="a70a7-163">Po pierwsze, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , gdzie `| K_E | = 192 bits` i `| K_H | = 160 bits` według określonych algorytmów.</span><span class="sxs-lookup"><span data-stu-id="a70a7-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="a70a7-164">Ten lider prowadzi do `K_E = A219..E2BB` i `K_H = DC4A..B464` w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a70a7-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="a70a7-165">Następnie należy obliczyć `Enc_CBC (K_E, IV, "")` dla algorytmu 3DES-192-CBC `IV = 0*` i `K_E` jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="a70a7-166">Następnie Oblicz `MAC(K_H, "")` dla HMACSHA1 podaną `K_H` powyżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="a70a7-167">Spowoduje to utworzenie pełnego nagłówka kontekstu, który jest odciskiem palca pary szyfrowany algorytm szyfrowania (3DES-192-CBC Encryption + HMACSHA1 Validation), jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="a70a7-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="a70a7-168">Składniki są podzielone w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a70a7-168">The components break down as follows:</span></span>

* <span data-ttu-id="a70a7-169">znacznik `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="a70a7-170">Długość klucza szyfrowania bloku `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="a70a7-171">rozmiar bloku szyfrowania bloku `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="a70a7-172">Długość klucza HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="a70a7-173">rozmiar podsumowania HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="a70a7-174">blokowe szyfrowanie danych wyjściowych szyfrowania `(AB B1 - E1 0E)` i</span><span class="sxs-lookup"><span data-stu-id="a70a7-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="a70a7-175">PRF dane wyjściowe HMAC `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="a70a7-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="a70a7-176">Szyfrowanie i tryb Galois/uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="a70a7-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="a70a7-177">Nagłówek kontekstu składa się z następujących składników:</span><span class="sxs-lookup"><span data-stu-id="a70a7-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="a70a7-178">[16 bitów] Wartość 00 01, która jest znacznikiem znaczenia "GCM Encryption + Authentication".</span><span class="sxs-lookup"><span data-stu-id="a70a7-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="a70a7-179">[32 bitów] Długość klucza (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="a70a7-180">[32 bitów] Rozmiar jednorazowy (w bajtach, big-endian) używany podczas operacji uwierzytelniania uwierzytelnionego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="a70a7-181">(W przypadku systemu ten problem jest ustalony w rozmiarze nonce = 96 bitów).</span><span class="sxs-lookup"><span data-stu-id="a70a7-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="a70a7-182">[32 bitów] Rozmiar bloku (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="a70a7-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="a70a7-183">(W przypadku GCM jest to ustalone w rozmiarze bloku = 128 bitów).</span><span class="sxs-lookup"><span data-stu-id="a70a7-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="a70a7-184">[32 bitów] Rozmiar znacznika uwierzytelniania (w bajtach, big-endian) generowany przez uwierzytelnioną funkcję szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="a70a7-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="a70a7-185">(W przypadku systemu ten problem jest ustalany w rozmiarze znacznika = 128 bitów).</span><span class="sxs-lookup"><span data-stu-id="a70a7-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="a70a7-186">[128 bitów] Tag `Enc_GCM (K_E, nonce, "")` , który jest wyjściem algorytmu szyfrowania bloku symetrycznego, z uwzględnieniem pustego ciągu wejściowego i gdzie nonce to 96-bit All-zero Vector.</span><span class="sxs-lookup"><span data-stu-id="a70a7-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="a70a7-187">`K_E` jest tworzony przy użyciu takiego samego mechanizmu jak w scenariuszu uwierzytelniania CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="a70a7-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="a70a7-188">Jednak ponieważ nie ma nic `K_H` w tym miejscu, firma Microsoft ma głównie `| K_H | = 0` , a algorytm jest zwijany do poniższego formularza.</span><span class="sxs-lookup"><span data-stu-id="a70a7-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="a70a7-189">Przykład: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="a70a7-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="a70a7-190">Najpierw Let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , gdzie `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="a70a7-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="a70a7-191">Następnie Oblicz znacznik uwierzytelniania `Enc_GCM (K_E, nonce, "")` dla algorytmu AES-256-GCM `nonce = 096` i `K_E` tak jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="a70a7-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="a70a7-192">Spowoduje to utworzenie pełnego nagłówka kontekstu poniżej:</span><span class="sxs-lookup"><span data-stu-id="a70a7-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="a70a7-193">Składniki są podzielone w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a70a7-193">The components break down as follows:</span></span>

* <span data-ttu-id="a70a7-194">znacznik `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="a70a7-195">Długość klucza szyfrowania bloku `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="a70a7-196">rozmiar nonce `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="a70a7-197">rozmiar bloku szyfrowania bloku `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="a70a7-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="a70a7-198">rozmiar znacznika uwierzytelniania `(00 00 00 10)` i</span><span class="sxs-lookup"><span data-stu-id="a70a7-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="a70a7-199">Tag uwierzytelniania z uruchamiania szyfru blokowego `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="a70a7-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
