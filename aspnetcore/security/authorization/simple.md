---
title: Prosta autoryzacja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, w jaki sposób używać atrybutu Autoryzuj, aby ograniczyć dostęp do kontrolerów ASP.NET Core i akcji.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775638"
---
# <a name="simple-authorization-in-aspnet-core"></a>Prosta autoryzacja w ASP.NET Core

<a name="security-authorization-simple"></a>

Autoryzacja w MVC jest kontrolowana przez `AuthorizeAttribute` atrybut i jego różne parametry. W najprostszej sposób stosowanie `AuthorizeAttribute` atrybutu do kontrolera lub akcji ogranicza dostęp do kontrolera lub akcji do dowolnego uwierzytelnionego użytkownika.

Na przykład poniższy kod ogranicza dostęp do `AccountController` dowolnego uwierzytelnionego użytkownika.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Jeśli chcesz zastosować autoryzację do akcji, a nie kontrolera, Zastosuj `AuthorizeAttribute` atrybut do samej akcji:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Teraz tylko uwierzytelnieni użytkownicy mogą uzyskiwać dostęp `Logout` do funkcji.

Można również użyć atrybutu, `AllowAnonymous` aby zezwolić na dostęp nieuwierzytelnionym użytkownikom do poszczególnych akcji. Przykład:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Może to umożliwić tylko uwierzytelnionym użytkownikom `AccountController`, z wyjątkiem `Login` akcji, która jest dostępna dla wszystkich, niezależnie od ich uwierzytelnionego lub nieuwierzytelnionego/anonimowego stanu.

> [!WARNING]
> `[AllowAnonymous]`pomija wszystkie instrukcje autoryzacji. W `[Authorize]` przypadku łączenia `[AllowAnonymous]` i dowolnych `[Authorize]` atrybutów atrybuty zostaną zignorowane. Na przykład w przypadku zastosowania `[AllowAnonymous]` na poziomie kontrolera wszystkie `[Authorize]` atrybuty na tym samym kontrolerze (lub na dowolnej akcji w ramach tego elementu) zostaną zignorowane.
