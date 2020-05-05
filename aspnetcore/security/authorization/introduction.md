---
title: Wprowadzenie do autoryzacji w ASP.NET Core
author: rick-anderson
description: Poznaj podstawowe informacje dotyczące autoryzacji i sposobu działania autoryzacji w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: 241ef8b00e9dcbd1983d32edcd9c1db2eaa5c687
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777530"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Wprowadzenie do autoryzacji w ASP.NET Core

<a name="security-authorization-introduction"></a>

Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić. Na przykład użytkownik administracyjny może utworzyć bibliotekę dokumentów, dodać dokumenty, edytować dokumenty i je usunąć. Użytkownik niebędący użytkownikiem administracyjnym, który współpracuje z biblioteką, ma uprawnienia tylko do odczytu dokumentów.

Autoryzacja jest prostopadła i niezależna od uwierzytelniania. Autoryzacja wymaga jednak mechanizmu uwierzytelniania. Uwierzytelnianie to proces ustalania, kto jest użytkownikiem. Uwierzytelnianie może utworzyć jedną lub więcej tożsamości dla bieżącego użytkownika.

Aby uzyskać więcej informacji na temat uwierzytelniania w ASP.NET Core <xref:security/authentication/index>, zobacz.

## <a name="authorization-types"></a>Typy autoryzacji

Autoryzacja ASP.NET Core zapewnia prostą, deklaratywną [rolę](xref:security/authorization/roles) i bogaty model [oparty na zasadach](xref:security/authorization/policies) . Autoryzacja jest wyrażona w wymaganiach, a programy obsługi szacują oświadczenia użytkownika względem wymagań. Bezwzględne kontrole mogą opierać się na prostych zasadach lub zasadach, które obliczają zarówno tożsamość użytkownika, jak i właściwości zasobu, do którego użytkownik próbuje uzyskać dostęp.

## <a name="namespaces"></a>Namespaces

W `Microsoft.AspNetCore.Authorization` przestrzeni nazw znajdują `AuthorizeAttribute` się `AllowAnonymousAttribute` składniki autoryzacji, w tym atrybuty i.

Zapoznaj się z dokumentacją dotyczącą [prostej autoryzacji](xref:security/authorization/simple).
