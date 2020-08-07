---
title: Różne interfejsy API ochrony danych ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o interfejsie ISecret ochrony danych ASP.NET Core.
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913811"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="b885f-103">Różne interfejsy API ochrony danych ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b885f-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="b885f-104">Typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.</span><span class="sxs-lookup"><span data-stu-id="b885f-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="b885f-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="b885f-105">ISecret</span></span>

<span data-ttu-id="b885f-106">`ISecret`Interfejs reprezentuje wartość wpisu tajnego, na przykład materiał klucza kryptograficznego.</span><span class="sxs-lookup"><span data-stu-id="b885f-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="b885f-107">Zawiera następującą powierzchnię interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="b885f-107">It contains the following API surface:</span></span>

* <span data-ttu-id="b885f-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="b885f-108">`Length`: `int`</span></span>

* <span data-ttu-id="b885f-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="b885f-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="b885f-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="b885f-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="b885f-111">`WriteSecretIntoBuffer`Metoda wypełnia podany bufor wartością surowego klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="b885f-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="b885f-112">Przyczyną, że ten interfejs API przyjmuje bufor jako parametr zamiast zwracać `byte[]` bezpośrednio, jest to, że obiekt wywołujący może przypiąć obiekt buforu, ograniczając tajne ujawnienie do zarządzanego modułu wyrzucania elementów bezużytecznych.</span><span class="sxs-lookup"><span data-stu-id="b885f-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="b885f-113">`Secret`Typ jest konkretną implementacją `ISecret` , gdzie wartość klucza tajnego jest przechowywana w pamięci w procesie.</span><span class="sxs-lookup"><span data-stu-id="b885f-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="b885f-114">Na platformach systemu Windows wartość klucza tajnego jest szyfrowana za pośrednictwem [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="b885f-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
