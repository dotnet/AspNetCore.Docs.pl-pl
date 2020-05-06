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
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="709d9-103">Szczegóły uwierzytelnionego szyfrowania w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="709d9-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="709d9-104">Wywołania do IDataProtector. Protect są uwierzytelnianymi operacjami szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="709d9-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="709d9-105">Metoda Protect zapewnia poufność i autentyczność oraz jest związana z łańcuchem przeznaczenia, który został użyty do wygenerowania tego konkretnego wystąpienia IDataProtector z jego głównego IDataProtectionProvideru.</span><span class="sxs-lookup"><span data-stu-id="709d9-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="709d9-106">IDataProtector. Protect pobiera parametr zwykłego tekstu Byte [] i tworzy ładunek zabezpieczony bajtem [], którego format został opisany poniżej.</span><span class="sxs-lookup"><span data-stu-id="709d9-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="709d9-107">(Istnieje również Przeciążenie metody rozszerzenia, które pobiera parametr w postaci zwykłego tekstu i zwraca ładunek w postaci ciągu tekstowego.</span><span class="sxs-lookup"><span data-stu-id="709d9-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="709d9-108">Jeśli ten interfejs API jest używany, format chronionego ładunku nadal będzie miał poniższą strukturę, ale będzie [base64url](https://tools.ietf.org/html/rfc4648#section-5).</span><span class="sxs-lookup"><span data-stu-id="709d9-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="709d9-109">Format chronionego ładunku</span><span class="sxs-lookup"><span data-stu-id="709d9-109">Protected payload format</span></span>

<span data-ttu-id="709d9-110">Format chronionego ładunku składa się z trzech głównych składników:</span><span class="sxs-lookup"><span data-stu-id="709d9-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="709d9-111">32-bitowy nagłówek Magic, który identyfikuje wersję systemu ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="709d9-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="709d9-112">Identyfikator klucza 128-bitowego, który identyfikuje klucz używany do ochrony danego ładunku.</span><span class="sxs-lookup"><span data-stu-id="709d9-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="709d9-113">Pozostała część chronionego ładunku jest [specyficzna dla modułu szyfrującego hermetyzowanego przez ten klucz](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="709d9-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="709d9-114">W poniższym przykładzie klucz reprezentuje szyfrowanie AES-256-CBC + HMACSHA256, a ładunek jest dalej podzielona w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="709d9-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="709d9-115">Modyfikator klucza 128-bitowego.</span><span class="sxs-lookup"><span data-stu-id="709d9-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="709d9-116">Wektor inicjalizacji 128-bitowego.</span><span class="sxs-lookup"><span data-stu-id="709d9-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="709d9-117">48 bajtów AES-256-CBC danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="709d9-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="709d9-118">Tag uwierzytelniania HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="709d9-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="709d9-119">Przykładowy zabezpieczony ładunek jest przedstawiony poniżej.</span><span class="sxs-lookup"><span data-stu-id="709d9-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="709d9-120">W formacie ładunku powyżej pierwszych 32 bitów lub 4 bajty to magiczny nagłówek identyfikujący wersję (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="709d9-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="709d9-121">Następny 128 bitów lub 16 bajtów jest identyfikatorem klucza (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="709d9-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="709d9-122">Pozostała część zawiera ładunek i jest zależna od użytego formatu.</span><span class="sxs-lookup"><span data-stu-id="709d9-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="709d9-123">Wszystkie ładunki chronione w danym kluczu będą rozpoczynać się od tego samego, 20-bajtowego (Magiczna wartość, identyfikator klucza).</span><span class="sxs-lookup"><span data-stu-id="709d9-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="709d9-124">Administratorzy mogą używać tego faktu w celach diagnostycznych do przybliżonego momentu wygenerowania ładunku.</span><span class="sxs-lookup"><span data-stu-id="709d9-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="709d9-125">Na przykład powyższy ładunek odpowiada kluczowi {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="709d9-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="709d9-126">Jeśli po sprawdzeniu repozytorium kluczy okaże się, że Data aktywacji określonego klucza to 2015-01-01, a jej data wygaśnięcia to 2015-03-01, rozsądne jest założenie, że ładunek (jeśli nie naruszony) został wygenerowany w tym oknie, nadaje lub przyjmuje mały współczynnik Fudge po obu stronach.</span><span class="sxs-lookup"><span data-stu-id="709d9-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
