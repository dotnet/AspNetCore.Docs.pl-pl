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
ms.openlocfilehash: e9de92233468e9e07791df608b1c37ffb3b29949
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408503"
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

`Secret`Typ jest konkretną implementacją `ISecret` , gdzie wartość klucza tajnego jest przechowywana w pamięci w procesie. Na platformach systemu Windows wartość klucza tajnego jest szyfrowana za pośrednictwem [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
