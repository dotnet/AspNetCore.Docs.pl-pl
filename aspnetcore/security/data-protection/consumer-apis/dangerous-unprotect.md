---
title: Wyłącz ochronę ładunków, których klucze zostały odwołane w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wyłączyć ochronę danych, a następnie chronić klucze, które zostały odwołane w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 062703fc72ab4e515a99558b3316070ce1f83f79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776802"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Wyłącz ochronę ładunków, których klucze zostały odwołane w ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Interfejsy API ochrony danych ASP.NET Core nie są przede wszystkim przeznaczone do nieograniczonego trwałości poufnych ładunków. Inne technologie, takie jak [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) i [Azure Rights Management](/rights-management/) , są bardziej odpowiednie dla scenariusza nieograniczonego magazynu i mają odpowiadające im silne możliwości zarządzania kluczami. Oznacza to, że nie ma żadnych zakazów używania ASP.NET Core interfejsów API ochrony danych do długoterminowej ochrony poufnych danych. Klucze nigdy nie są usuwane z pierścienia kluczy, `IDataProtector.Unprotect` więc zawsze mogą odzyskiwać istniejące ładunki, o ile klucze są dostępne i prawidłowe.

Występuje jednak problem polegający na tym, że deweloper próbuje wyłączyć ochronę danych chronionych za pomocą odwołanego klucza, co `IDataProtector.Unprotect` spowoduje zgłoszenie wyjątku w tym przypadku. Może to być korzystne w przypadku ładunków krótkoterminowych lub przejściowych (takich jak tokeny uwierzytelniania), ponieważ te rodzaje ładunków mogą być w łatwy sposób odtworzone przez system, a w najgorszym miejscu może być wymagane zalogowanie się do osoby odwiedzającej. Jednak w przypadku utrwalonych ładunków, `Unprotect` których zgłoszenie może prowadzić do niedopuszczalnej utraty danych.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Aby obsłużyć scenariusz zezwalania na nieochrony ładunków, nawet w przypadku wycofanych kluczy, system ochrony danych zawiera `IPersistedDataProtector` typ. Aby uzyskać wystąpienie `IPersistedDataProtector`elementu, wystarczy uzyskać wystąpienie `IDataProtector` w normalny sposób i spróbować rzutować `IDataProtector` do. `IPersistedDataProtector`

> [!NOTE]
> Nie wszystkie `IDataProtector` wystąpienia mogą być rzutowane `IPersistedDataProtector`na. Deweloperzy powinni używać języka C# jako operatora lub podobnego, aby uniknąć wyjątków czasu wykonywania spowodowanych przez nieprawidłowe rzutowania i powinny być przygotowane w celu odpowiedniego obsłużenia przypadku awarii.

`IPersistedDataProtector`uwidacznia następującą powierzchnię interfejsu API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Ten interfejs API pobiera chroniony ładunek (jako tablicę bajtową) i zwraca ładunek niechroniony. Brak przeciążenia opartego na ciągach. Dwa parametry out są następujące.

* `requiresMigration`: zostanie ustawiona na wartość true, jeśli klucz używany do ochrony tego ładunku nie jest już aktywnym kluczem domyślnym, np. klucz używany do ochrony tego ładunku jest stary i przeprowadzono kluczową operację wycofywania. Obiekt wywołujący może chcieć rozważyć ponowne włączenie ochrony ładunku w zależności od potrzeb firmy.

* `wasRevoked`: zostanie ustawiona na wartość true, jeśli klucz używany do ochrony tego ładunku został odwołany.

>[!WARNING]
> Podczas przekazywania `ignoreRevocationErrors: true` do `DangerousUnprotect` metody należy zachować szczególną ostrożność. Jeśli po wywołaniu tej metody `wasRevoked` wartość jest równa true, klucz używany do ochrony tego ładunku został odwołany, a autentyczność ładunku powinna być traktowana jako podejrzana. W takim przypadku Kontynuuj działanie w niechronionym ładunku, jeśli masz osobną gwarancję, że jest ona autentyczna, np. pochodzi z bezpiecznej bazy danych, a nie jest wysyłana przez niezaufanego klienta sieci Web.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
