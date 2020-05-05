---
title: Nagłówki kontekstu w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji nagłówków kontekstu ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 381cc137d1de87e87f36c3b32a6a551a318ed3cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776958"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="8abf0-103">Nagłówki kontekstu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8abf0-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="8abf0-104">Tło i teorii</span><span class="sxs-lookup"><span data-stu-id="8abf0-104">Background and theory</span></span>

<span data-ttu-id="8abf0-105">W systemie ochrony danych "klucz" oznacza obiekt, który może zapewniać uwierzytelnione usługi szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="8abf0-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="8abf0-106">Każdy klucz jest identyfikowany za pomocą unikatowego identyfikatora (GUID) i zawiera informacje o algorytmach IT i materiał entropic.</span><span class="sxs-lookup"><span data-stu-id="8abf0-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="8abf0-107">Należy zastanowić się, że każdy klucz ma unikatową entropię, ale system nie może wymusić tego działania, a ponadto musimy uwzględnić deweloperów, którzy mogą ręcznie zmienić pierścień kluczy, modyfikując informacje o algorytmach istniejącego klucza w pęku kluczy.</span><span class="sxs-lookup"><span data-stu-id="8abf0-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="8abf0-108">W celu osiągnięcia naszych wymagań w zakresie bezpieczeństwa w tym przypadku system ochrony danych ma koncepcję [kryptografii kryptograficznej](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), która umożliwia bezpieczne używanie jednej wartości entropic w wielu algorytmach kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="8abf0-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="8abf0-109">Większość systemów, które obsługują elastyczność kryptograficzną, to przez uwzględnienie pewnych informacji identyfikujących algorytm wewnątrz ładunku.</span><span class="sxs-lookup"><span data-stu-id="8abf0-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="8abf0-110">Identyfikator OID algorytmu jest zwykle dobrym kandydatem do tego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="8abf0-111">Jednak jeden z nich wystąpił, że istnieje wiele sposobów na określenie tego samego algorytmu: "AES" (CNG), a zarządzane algorytmy AES, AesManaged, AesCryptoServiceProvider, AesCng i RijndaelManaged (dane o określonych parametrach) są faktycznie tymi samymi, a musimy zachować mapowanie wszystkich tych elementów do właściwego identyfikatora OID.</span><span class="sxs-lookup"><span data-stu-id="8abf0-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="8abf0-112">Jeśli deweloper chciał udostępnić niestandardowy algorytm (lub nawet inną implementację algorytmu AES), musi powiedzieć nas o jego identyfikatorze OID.</span><span class="sxs-lookup"><span data-stu-id="8abf0-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="8abf0-113">Ten dodatkowy krok rejestracji powoduje, że konfiguracja systemu jest szczególnie bolesnym.</span><span class="sxs-lookup"><span data-stu-id="8abf0-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="8abf0-114">Przechodzenie przez nas z powrotem zadecydował się, że wystąpił problem z niewłaściwym kierunkiem.</span><span class="sxs-lookup"><span data-stu-id="8abf0-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="8abf0-115">Identyfikator OID informuje o tym, czym jest algorytm, ale nie jest to naprawdę ważne.</span><span class="sxs-lookup"><span data-stu-id="8abf0-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="8abf0-116">Jeśli musimy bezpiecznie użyć pojedynczej wartości entropic w dwóch różnych algorytmach, nie jest to konieczne, aby wiedzieć, jakie algorytmy rzeczywiście są.</span><span class="sxs-lookup"><span data-stu-id="8abf0-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="8abf0-117">Zadbajmy o to, jak działa.</span><span class="sxs-lookup"><span data-stu-id="8abf0-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="8abf0-118">Każdy algorytm szyfrowania bloku symetrycznego znośnego jest również silnie pseudolosowych permutacji (PRP): Popraw dane wejściowe (klucz, tryb łańcucha, IV, zwykły tekst) i dane wyjściowe tekstu szyfrowanego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="8abf0-119">Podobnie każda znośnegoa funkcja skrótu jest również silną funkcją pseudolosowych (PRF) i ze stałym zestawem danych wejściowych, która będzie zależeć od dowolnej innej funkcji skrótu z ustawioną instrukcją.</span><span class="sxs-lookup"><span data-stu-id="8abf0-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="8abf0-120">Używamy tej koncepcji mocnych PRPs i PRFs do kompilowania nagłówka kontekstu.</span><span class="sxs-lookup"><span data-stu-id="8abf0-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="8abf0-121">Ten nagłówek kontekstu zasadniczo działa jako stabilny odcisk palca dla algorytmów używanych dla danej operacji i zapewnia elastyczność kryptograficzną wymaganą przez system ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="8abf0-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="8abf0-122">Ten nagłówek jest powtarzalny i używany później jako część [procesu wyprowadzania podklucza](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="8abf0-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="8abf0-123">Istnieją dwa różne sposoby tworzenia nagłówka kontekstu w zależności od trybów działania podstawowych algorytmów.</span><span class="sxs-lookup"><span data-stu-id="8abf0-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="8abf0-124">Szyfrowanie w trybie CBC + uwierzytelnianie HMAC</span><span class="sxs-lookup"><span data-stu-id="8abf0-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="8abf0-125">Nagłówek kontekstu składa się z następujących składników:</span><span class="sxs-lookup"><span data-stu-id="8abf0-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="8abf0-126">[16 bitów] Wartość 00 00, która jest znacznikiem znaczenia "CBC Encryption + HMAC Authentication".</span><span class="sxs-lookup"><span data-stu-id="8abf0-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="8abf0-127">[32 bitów] Długość klucza (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="8abf0-128">[32 bitów] Rozmiar bloku (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="8abf0-129">[32 bitów] Długość klucza (w bajtach, big-endian) algorytmu HMAC.</span><span class="sxs-lookup"><span data-stu-id="8abf0-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="8abf0-130">(Obecnie rozmiar klucza zawsze jest zgodny z rozmiarem podsumowania).</span><span class="sxs-lookup"><span data-stu-id="8abf0-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="8abf0-131">[32 bitów] Rozmiar podsumowania (w bajtach, big-endian) algorytmu HMAC.</span><span class="sxs-lookup"><span data-stu-id="8abf0-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="8abf0-132">EncCBC (K_E, IV, ""), który jest wyjściem algorytmu szyfrowania symetrycznego, podano puste dane wejściowe i gdzie IV jest wektorem "All-zero".</span><span class="sxs-lookup"><span data-stu-id="8abf0-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="8abf0-133">Konstrukcja K_E została opisana poniżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="8abf0-134">MAC (K_H, ""), który jest wyjściem algorytmu HMAC, z uwzględnieniem pustego ciągu wejściowego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="8abf0-135">Konstrukcja K_H została opisana poniżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="8abf0-136">Najlepszym rozwiązaniem jest przekazanie wszystkich wektorów o wartości zero dla K_E i K_H.</span><span class="sxs-lookup"><span data-stu-id="8abf0-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="8abf0-137">Jednak chcemy unikać sytuacji, w której podstawowy algorytm sprawdza obecność słabych kluczy przed wykonaniem jakiejkolwiek operacji (w szczególności DES i 3DES), która wyklucza użycie prostego lub powtarzalnego wzorca, takiego jak wektor "All-zero".</span><span class="sxs-lookup"><span data-stu-id="8abf0-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="8abf0-138">Zamiast tego używamy NIST SP800-108 KDF w trybie licznika (zobacz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), sek. 5,1) z kluczem o zerowej długości, etykiecie i kontekście oraz HMACSHA512 jako bazowego PRF.</span><span class="sxs-lookup"><span data-stu-id="8abf0-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="8abf0-139">Firma Microsoft dziedziczy | K_E | + | K_H | bajty danych wyjściowych, a następnie Rozłóż wynik do K_E i K_H siebie.</span><span class="sxs-lookup"><span data-stu-id="8abf0-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="8abf0-140">Matematycznie jest to reprezentowane w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="8abf0-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="8abf0-141">(K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="8abf0-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="8abf0-142">Przykład: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="8abf0-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="8abf0-143">Przykładowo Rozważmy przypadek, w którym algorytm szyfrowania bloku symetrycznego to AES-192-CBC, a algorytm walidacji to HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="8abf0-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="8abf0-144">System wygeneruje nagłówek kontekstu, wykonując poniższe kroki.</span><span class="sxs-lookup"><span data-stu-id="8abf0-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="8abf0-145">Najpierw Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = ""), gdzie | K_E | = 192 bitów i | K_H | = 256 bitów według określonych algorytmów.</span><span class="sxs-lookup"><span data-stu-id="8abf0-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="8abf0-146">To prowadzi do K_E = 5BB6.. 21DD i K_H = A04A.. 00A9 w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8abf0-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="8abf0-147">Następnie Enc_CBC obliczeniowe (K_E, IV, "") dla algorytmu AES-192-CBC przyznany IV = 0 \* i K_E jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="8abf0-148">wynik: = F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="8abf0-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="8abf0-149">Następnie należy obliczyć komputery MAC (K_H "") dla HMACSHA256 K_H jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="8abf0-150">wynik: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="8abf0-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="8abf0-151">Spowoduje to utworzenie pełnego nagłówka kontekstu poniżej:</span><span class="sxs-lookup"><span data-stu-id="8abf0-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="8abf0-152">Ten nagłówek kontekstu jest odciskiem palca uwierzytelnionej pary algorytmu szyfrowania (AES-192-CBC Encryption + HMACSHA256 Validation).</span><span class="sxs-lookup"><span data-stu-id="8abf0-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="8abf0-153">Składniki, zgodnie z [powyższym](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) opisem, to:</span><span class="sxs-lookup"><span data-stu-id="8abf0-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="8abf0-154">znacznik (00 00)</span><span class="sxs-lookup"><span data-stu-id="8abf0-154">the marker (00 00)</span></span>

* <span data-ttu-id="8abf0-155">Długość klucza szyfrowania bloku (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="8abf0-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="8abf0-156">rozmiar bloku szyfrowania bloku (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="8abf0-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="8abf0-157">Długość klucza HMAC (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="8abf0-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="8abf0-158">rozmiar podsumowania HMAC (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="8abf0-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="8abf0-159">blok danych wyjściowych szyfrowania z szyfrowaniem (F4 74-DB 6F) i</span><span class="sxs-lookup"><span data-stu-id="8abf0-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="8abf0-160">PRF dane wyjściowe HMAC (D4 79-end).</span><span class="sxs-lookup"><span data-stu-id="8abf0-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="8abf0-161">Nagłówek kontekstu uwierzytelniania CBC + HMAC jest zbudowany w taki sam sposób, niezależnie od tego, czy implementacje algorytmów są dostarczane przez system Windows CNG, czy przez zarządzane typy SymmetricAlgorithm i KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="8abf0-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="8abf0-162">Pozwala to aplikacjom działającym w różnych systemach operacyjnych na niezawodne generowanie tego samego nagłówka kontekstu, mimo że implementacje algorytmów różnią się między systemów operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="8abf0-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="8abf0-163">(W przypadku KeyedHashAlgorithm nie musi być prawidłowym elementem HMAC.</span><span class="sxs-lookup"><span data-stu-id="8abf0-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="8abf0-164">Może to być dowolny typ algorytmu wyznaczania wartości skrótu.)</span><span class="sxs-lookup"><span data-stu-id="8abf0-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="8abf0-165">Przykład: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="8abf0-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="8abf0-166">Najpierw Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = ""), gdzie | K_E | = 192 bitów i | K_H | = 160 bitów według określonych algorytmów.</span><span class="sxs-lookup"><span data-stu-id="8abf0-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="8abf0-167">To prowadzi do K_E = A219.. E2BB i K_H = DC4A.. B464 w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8abf0-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="8abf0-168">Następnie Enc_CBC obliczeniowe (K_E, IV, "") dla algorytmu 3DES-192-CBC przyznany IV = 0 \* i K_E jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="8abf0-169">wynik: = ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="8abf0-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="8abf0-170">Następnie należy obliczyć komputery MAC (K_H "") dla HMACSHA1 K_H jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="8abf0-171">wynik: = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="8abf0-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="8abf0-172">Spowoduje to utworzenie pełnego nagłówka kontekstu, który jest odciskiem palca pary szyfrowany algorytm szyfrowania (3DES-192-CBC Encryption + HMACSHA1 Validation), jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="8abf0-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="8abf0-173">Składniki są podzielone w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="8abf0-173">The components break down as follows:</span></span>

* <span data-ttu-id="8abf0-174">znacznik (00 00)</span><span class="sxs-lookup"><span data-stu-id="8abf0-174">the marker (00 00)</span></span>

* <span data-ttu-id="8abf0-175">Długość klucza szyfrowania bloku (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="8abf0-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="8abf0-176">rozmiar bloku szyfrowania bloku (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="8abf0-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="8abf0-177">Długość klucza HMAC (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="8abf0-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="8abf0-178">rozmiar podsumowania HMAC (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="8abf0-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="8abf0-179">blok danych wyjściowych szyfrowania z szyfrowaniem (AB B1-E1 0E) i</span><span class="sxs-lookup"><span data-stu-id="8abf0-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="8abf0-180">PRF dane wyjściowe HMAC (76 EB-end).</span><span class="sxs-lookup"><span data-stu-id="8abf0-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="8abf0-181">Szyfrowanie i tryb Galois/uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="8abf0-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="8abf0-182">Nagłówek kontekstu składa się z następujących składników:</span><span class="sxs-lookup"><span data-stu-id="8abf0-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="8abf0-183">[16 bitów] Wartość 00 01, która jest znacznikiem znaczenia "GCM Encryption + Authentication".</span><span class="sxs-lookup"><span data-stu-id="8abf0-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="8abf0-184">[32 bitów] Długość klucza (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="8abf0-185">[32 bitów] Rozmiar jednorazowy (w bajtach, big-endian) używany podczas operacji uwierzytelniania uwierzytelnionego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="8abf0-186">(W przypadku systemu ten problem jest ustalony w rozmiarze nonce = 96 bitów).</span><span class="sxs-lookup"><span data-stu-id="8abf0-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="8abf0-187">[32 bitów] Rozmiar bloku (w bajtach, big-endian) algorytmu szyfrowania symetrycznego.</span><span class="sxs-lookup"><span data-stu-id="8abf0-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="8abf0-188">(W przypadku GCM jest to ustalone w rozmiarze bloku = 128 bitów).</span><span class="sxs-lookup"><span data-stu-id="8abf0-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="8abf0-189">[32 bitów] Rozmiar znacznika uwierzytelniania (w bajtach, big-endian) generowany przez uwierzytelnioną funkcję szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="8abf0-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="8abf0-190">(W przypadku systemu ten problem jest ustalany w rozmiarze znacznika = 128 bitów).</span><span class="sxs-lookup"><span data-stu-id="8abf0-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="8abf0-191">[128 bitów] Tag Enc_GCM (K_E, nonce, ""), który jest wyjściem algorytmu szyfrowania bloku symetrycznego, z uwzględnieniem pustego ciągu wejściowego i gdzie nonce to 96-bit All-zero wektor.</span><span class="sxs-lookup"><span data-stu-id="8abf0-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="8abf0-192">K_E jest uzyskiwany przy użyciu tego samego mechanizmu co w scenariuszu uwierzytelniania CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="8abf0-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="8abf0-193">Ponieważ jednak w tym miejscu nie ma K_H, firma Microsoft ma głównie | K_H | = 0, a algorytm jest zwijany do poniższego formularza.</span><span class="sxs-lookup"><span data-stu-id="8abf0-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="8abf0-194">K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="8abf0-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="8abf0-195">Przykład: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="8abf0-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="8abf0-196">Najpierw Zezwól na K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", context = ""), gdzie | K_E | = 256 bitów.</span><span class="sxs-lookup"><span data-stu-id="8abf0-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="8abf0-197">K_E: = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="8abf0-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="8abf0-198">Następnie Oblicz tag uwierzytelniania Enc_GCM (K_E, nonce, "") dla algorytmu AES-256-GCM danego identyfikatora nonce = 096 i K_E jak powyżej.</span><span class="sxs-lookup"><span data-stu-id="8abf0-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="8abf0-199">wynik: = E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="8abf0-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="8abf0-200">Spowoduje to utworzenie pełnego nagłówka kontekstu poniżej:</span><span class="sxs-lookup"><span data-stu-id="8abf0-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="8abf0-201">Składniki są podzielone w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="8abf0-201">The components break down as follows:</span></span>

* <span data-ttu-id="8abf0-202">znacznik (00 01)</span><span class="sxs-lookup"><span data-stu-id="8abf0-202">the marker (00 01)</span></span>

* <span data-ttu-id="8abf0-203">Długość klucza szyfrowania bloku (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="8abf0-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="8abf0-204">rozmiar jednorazowy (00 00 00 0C)</span><span class="sxs-lookup"><span data-stu-id="8abf0-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="8abf0-205">rozmiar bloku szyfrowania bloku (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="8abf0-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="8abf0-206">rozmiar znacznika uwierzytelniania (00 00 00 10) i</span><span class="sxs-lookup"><span data-stu-id="8abf0-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="8abf0-207">Tag uwierzytelniania z uruchamiania szyfru blokowego (E7 DC-end).</span><span class="sxs-lookup"><span data-stu-id="8abf0-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
