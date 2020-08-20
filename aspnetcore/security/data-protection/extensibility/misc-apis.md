---
title: Różne interfejsy API ochrony danych ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o interfejsie ISecret ochrony danych ASP.NET Core.
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 947c6eb62550d325492365ece84d45a14845888f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630916"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="f0832-103">Różne interfejsy API ochrony danych ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0832-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="f0832-104">Typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.</span><span class="sxs-lookup"><span data-stu-id="f0832-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="f0832-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="f0832-105">ISecret</span></span>

<span data-ttu-id="f0832-106">`ISecret`Interfejs reprezentuje wartość wpisu tajnego, na przykład materiał klucza kryptograficznego.</span><span class="sxs-lookup"><span data-stu-id="f0832-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="f0832-107">Zawiera następującą powierzchnię interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="f0832-107">It contains the following API surface:</span></span>

* <span data-ttu-id="f0832-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="f0832-108">`Length`: `int`</span></span>

* <span data-ttu-id="f0832-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="f0832-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="f0832-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="f0832-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="f0832-111">`WriteSecretIntoBuffer`Metoda wypełnia podany bufor wartością surowego klucza tajnego.</span><span class="sxs-lookup"><span data-stu-id="f0832-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="f0832-112">Przyczyną, że ten interfejs API przyjmuje bufor jako parametr zamiast zwracać `byte[]` bezpośrednio, jest to, że obiekt wywołujący może przypiąć obiekt buforu, ograniczając tajne ujawnienie do zarządzanego modułu wyrzucania elementów bezużytecznych.</span><span class="sxs-lookup"><span data-stu-id="f0832-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="f0832-113">`Secret`Typ jest konkretną implementacją `ISecret` , gdzie wartość klucza tajnego jest przechowywana w pamięci w procesie.</span><span class="sxs-lookup"><span data-stu-id="f0832-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="f0832-114">Na platformach systemu Windows wartość klucza tajnego jest szyfrowana za pośrednictwem [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="f0832-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
