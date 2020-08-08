---
title: Różne interfejsy API ochrony danych ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o interfejsie ISecret ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
ms.openlocfilehash: 5ab8226779fb4209a7254b95eccac4be2d26b10d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019043"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Różne interfejsy API ochrony danych ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typy implementujące jeden z następujących interfejsów powinny być bezpieczne dla wątków dla wielu wywołań.

## <a name="isecret"></a>ISecret

`ISecret`Interfejs reprezentuje wartość wpisu tajnego, na przykład materiał klucza kryptograficznego. Zawiera następującą powierzchnię interfejsu API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`Metoda wypełnia podany bufor wartością surowego klucza tajnego. Przyczyną, że ten interfejs API przyjmuje bufor jako parametr zamiast zwracać `byte[]` bezpośrednio, jest to, że obiekt wywołujący może przypiąć obiekt buforu, ograniczając tajne ujawnienie do zarządzanego modułu wyrzucania elementów bezużytecznych.

`Secret`Typ jest konkretną implementacją `ISecret` , gdzie wartość klucza tajnego jest przechowywana w pamięci w procesie. Na platformach systemu Windows wartość klucza tajnego jest szyfrowana za pośrednictwem [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).
