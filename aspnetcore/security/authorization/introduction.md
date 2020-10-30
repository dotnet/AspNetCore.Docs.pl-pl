---
title: Wprowadzenie do autoryzacji w ASP.NET Core
author: rick-anderson
description: Poznaj podstawowe informacje dotyczące autoryzacji i sposobu działania autoryzacji w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060251"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Wprowadzenie do autoryzacji w ASP.NET Core

<a name="security-authorization-introduction"></a>

Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić. Na przykład użytkownik administracyjny może utworzyć bibliotekę dokumentów, dodać dokumenty, edytować dokumenty i je usunąć. Użytkownik niebędący użytkownikiem administracyjnym, który współpracuje z biblioteką, ma uprawnienia tylko do odczytu dokumentów.

Autoryzacja jest prostopadła i niezależna od uwierzytelniania. Autoryzacja wymaga jednak mechanizmu uwierzytelniania. Uwierzytelnianie to proces ustalania, kto jest użytkownikiem. Uwierzytelnianie może utworzyć jedną lub więcej tożsamości dla bieżącego użytkownika.

Aby uzyskać więcej informacji na temat uwierzytelniania w ASP.NET Core, zobacz <xref:security/authentication/index> .

## <a name="authorization-types"></a>Typy autoryzacji

Autoryzacja ASP.NET Core zapewnia prostą, deklaratywną [rolę](xref:security/authorization/roles) i bogaty model [oparty na zasadach](xref:security/authorization/policies) . Autoryzacja jest wyrażona w wymaganiach, a programy obsługi szacują oświadczenia użytkownika względem wymagań. Bezwzględne kontrole mogą opierać się na prostych zasadach lub zasadach, które obliczają zarówno tożsamość użytkownika, jak i właściwości zasobu, do którego użytkownik próbuje uzyskać dostęp.

## <a name="namespaces"></a>Przestrzenie nazw

`AuthorizeAttribute` `AllowAnonymousAttribute` W przestrzeni nazw znajdują się składniki autoryzacji, w tym atrybuty i `Microsoft.AspNetCore.Authorization` .

Zapoznaj się z dokumentacją dotyczącą [prostej autoryzacji](xref:security/authorization/simple).
