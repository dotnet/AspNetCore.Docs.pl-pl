---
title: Ciągi przeznaczenie w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ciągi przeznaczenia są używane w interfejsach API ochrony danych ASP.NET Core.
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: b52961fd33ce2d3708754f73ea38456d8d5f8f3c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404291"
---
# <a name="purpose-strings-in-aspnet-core"></a>Ciągi przeznaczenie w ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Składniki, które zużywają, `IDataProtectionProvider` muszą przekazać do metody unikatowy parametr *celów* `CreateProtector` . *Parametr* cele jest związany z bezpieczeństwem systemu ochrony danych, ponieważ zapewnia on izolację między konsumentami kryptograficznymi, nawet jeśli główne klucze kryptograficzne są takie same.

Gdy odbiorca określi przeznaczenie, ciąg celu jest używany razem z głównymi kluczami kryptograficznymi w celu uzyskania kluczy kryptograficznych, które są unikatowe dla tego klienta. Spowoduje to oddzielenie konsumenta od wszystkich innych użytkowników kryptograficznych w aplikacji: żaden inny składnik nie może odczytywać swoich ładunków i nie może odczytać żadnych innych ładunków składnika. Ta izolacja również renderuje w sposób niewykonalny cały poziom ataku na składnik.

![Przykład diagramu przeznaczenie](purpose-strings/_static/purposes.png)

Na powyższym diagramie `IDataProtector` wystąpienia a i B **nie mogą** odczytywać wszystkich ładunków, tylko ich własne.

Ciąg celu nie musi być tajny. Powinien być po prostu unikatowy w sensie, że żaden inny dobrze działający składnik nie będzie miał tego samego ciągu celu.

>[!TIP]
> Użycie przestrzeni nazw i nazwy typu składnika korzystającego z interfejsów API ochrony danych jest dobrą regułą kciuka, jak w przypadku te informacje nigdy nie będą powodować konfliktów.
>
>Składnik firmy Contoso, który jest odpowiedzialny za tokeny okaziciela minting, może używać contoso. Security. BearerToken jako ciągu celu. Lub — jeszcze lepsze — może używać contoso. Security. BearerToken. v1 jako ciągu celu. Dołączenie numeru wersji umożliwia użycie w przyszłości firmy Contoso. Security. BearerToken. v2, a różne wersje są całkowicie odizolowane od siebie.

Ponieważ parametr cele `CreateProtector` jest tablicą ciągów, zamiast tego można określić powyższe polecenie jako `[ "Contoso.Security.BearerToken", "v1" ]` . Pozwala to na utworzenie hierarchii celów i otwarcie możliwości scenariuszy wielodostępnych z systemem ochrony danych.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Składniki nie powinny zezwalać na dostęp niezaufanych użytkowników jako jedyne źródło danych wejściowych dla łańcucha celów.
>
>Rozważmy na przykład składnik contoso. Messaging. SecureMessage, który jest odpowiedzialny za przechowywanie bezpiecznych komunikatów. Jeśli składnik zabezpieczonych komunikatów był wywoływany `CreateProtector([ username ])` , złośliwy użytkownik może utworzyć konto o nazwie "contoso. Security. BearerToken" w celu uzyskania składnika do wywołania `CreateProtector([ "Contoso.Security.BearerToken" ])` , a tym samym przypadkowo spowodować, że bezpieczny system obsługi komunikatów mennic ładunki, które mogą być postrzegane jako tokeny uwierzytelniania.
>
>Lepszym łańcuchem zastosowań dla składnika obsługi komunikatów jest `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , który zapewnia właściwą izolację.

Izolacja zapewniana przez program oraz zachowania `IDataProtectionProvider` , `IDataProtector` i są następujące:

* Dla danego `IDataProtectionProvider` obiektu `CreateProtector` Metoda utworzy `IDataProtector` obiekt jednoznacznie powiązany zarówno z `IDataProtectionProvider` obiektem, który go utworzył, jak i parametrem cele, który został przekazany do metody.

* Parametr celu nie może mieć wartości null. (Jeśli cele są określone jako tablica, oznacza to, że tablica nie może mieć zerowej długości, a wszystkie elementy tablicy muszą być inne niż null.) Pusty cel ciągu jest technicznie dozwolony, ale nie jest odradzany.

* Dwa cele argumentów są równoważne, jeśli i tylko wtedy, gdy zawierają te same ciągi (przy użyciu porównującej porządkowej) w tej samej kolejności. Argument pojedynczego celu jest równoważny z odpowiadającą mu tablicą celów pojedynczego elementu.

* Dwa `IDataProtector` obiekty są równoważne w przypadku i tylko wtedy, gdy są tworzone z obiektów równoważnych `IDataProtectionProvider` z parametrami równoważnych celów.

* Dla danego `IDataProtector` obiektu wywołanie zwróci `Unprotect(protectedData)` oryginalny element `unprotectedData` if i tylko wtedy, gdy `protectedData := Protect(unprotectedData)` dla obiektu równoważnego `IDataProtector` .

> [!NOTE]
> Nie rozważamy przypadków, w których jakiś składnik celowo wybiera ciąg celu, który jest znany w konflikcie z innym składnikiem. Taki składnik powinien być uważany za złośliwy i ten system nie jest przeznaczony do zapewnienia gwarancji bezpieczeństwa w przypadku, gdy złośliwy kod jest już uruchomiony w procesie roboczym.
