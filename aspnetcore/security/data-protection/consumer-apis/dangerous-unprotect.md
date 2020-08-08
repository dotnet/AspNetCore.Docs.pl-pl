---
title: Wyłącz ochronę ładunków, których klucze zostały odwołane w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wyłączyć ochronę danych, a następnie chronić klucze, które zostały odwołane w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 29bd9010bc9f2d9799d079e44e7b3faa359699b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019719"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Wyłącz ochronę ładunków, których klucze zostały odwołane w ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Interfejsy API ochrony danych ASP.NET Core nie są przede wszystkim przeznaczone do nieograniczonego trwałości poufnych ładunków. Inne technologie, takie jak [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) i [Azure Rights Management](/rights-management/) , są bardziej odpowiednie dla scenariusza nieograniczonego magazynu i mają odpowiadające im silne możliwości zarządzania kluczami. Oznacza to, że nie ma żadnych zakazów używania ASP.NET Core interfejsów API ochrony danych do długoterminowej ochrony poufnych danych. Klucze nigdy nie są usuwane z pierścienia kluczy, więc `IDataProtector.Unprotect` zawsze mogą odzyskiwać istniejące ładunki, o ile klucze są dostępne i prawidłowe.

Występuje jednak problem polegający na tym, że deweloper próbuje wyłączyć ochronę danych chronionych za pomocą odwołanego klucza, co `IDataProtector.Unprotect` spowoduje zgłoszenie wyjątku w tym przypadku. Może to być korzystne w przypadku ładunków krótkoterminowych lub przejściowych (takich jak tokeny uwierzytelniania), ponieważ te rodzaje ładunków mogą być w łatwy sposób odtworzone przez system, a w najgorszym miejscu może być wymagane zalogowanie się do osoby odwiedzającej. Jednak w przypadku utrwalonych ładunków, których `Unprotect` Zgłoszenie może prowadzić do niedopuszczalnej utraty danych.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Aby obsłużyć scenariusz zezwalania na nieochrony ładunków, nawet w przypadku wycofanych kluczy, system ochrony danych zawiera `IPersistedDataProtector` Typ. Aby uzyskać wystąpienie elementu `IPersistedDataProtector` , wystarczy uzyskać wystąpienie `IDataProtector` w normalny sposób i spróbować rzutować `IDataProtector` do `IPersistedDataProtector` .

> [!NOTE]
> Nie wszystkie `IDataProtector` wystąpienia mogą być rzutowane na `IPersistedDataProtector` . Deweloperzy powinni używać języka C# jako operatora lub podobnego, aby uniknąć wyjątków czasu wykonywania spowodowanych przez nieprawidłowe rzutowania i powinny być przygotowane w celu odpowiedniego obsłużenia przypadku awarii.

`IPersistedDataProtector`uwidacznia następującą powierzchnię interfejsu API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Ten interfejs API pobiera chroniony ładunek (jako tablicę bajtową) i zwraca ładunek niechroniony. Brak przeciążenia opartego na ciągach. Dwa parametry out są następujące.

* `requiresMigration`: zostanie ustawiona na wartość true, jeśli klucz używany do ochrony tego ładunku nie jest już aktywnym kluczem domyślnym, np. klucz używany do ochrony tego ładunku jest stary i przeprowadzono kluczową operację wycofywania. Obiekt wywołujący może chcieć rozważyć ponowne włączenie ochrony ładunku w zależności od potrzeb firmy.

* `wasRevoked`: zostanie ustawiona na wartość true, jeśli klucz używany do ochrony tego ładunku został odwołany.

>[!WARNING]
> Podczas przekazywania do metody należy zachować szczególną ostrożność `ignoreRevocationErrors: true` `DangerousUnprotect` . Jeśli po wywołaniu tej metody `wasRevoked` wartość jest równa true, klucz używany do ochrony tego ładunku został odwołany, a autentyczność ładunku powinna być traktowana jako podejrzana. W takim przypadku Kontynuuj działanie w niechronionym ładunku, jeśli masz osobną gwarancję, że jest ona autentyczna, np. pochodzi z bezpiecznej bazy danych, a nie jest wysyłana przez niezaufanego klienta sieci Web.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
