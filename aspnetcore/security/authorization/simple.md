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
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272128"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="fa84f-103">Prosta autoryzacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa84f-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="fa84f-104">Autoryzacja w ASP.NET Core jest kontrolowana przy użyciu <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> i jego różnych parametrów.</span><span class="sxs-lookup"><span data-stu-id="fa84f-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="fa84f-105">W najprostszej postaci stosowanie `[Authorize]` atrybutu do kontrolera, akcji lub Razor strony ogranicza dostęp do tego składnika do dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fa84f-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="fa84f-106">Na przykład poniższy kod ogranicza dostęp do `AccountController` dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fa84f-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="fa84f-107">Jeśli chcesz zastosować autoryzację do akcji, a nie kontrolera, Zastosuj `AuthorizeAttribute` atrybut do samej akcji:</span><span class="sxs-lookup"><span data-stu-id="fa84f-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="fa84f-108">Teraz tylko uwierzytelnieni użytkownicy mogą uzyskiwać dostęp do `Logout` funkcji.</span><span class="sxs-lookup"><span data-stu-id="fa84f-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="fa84f-109">Można również użyć atrybutu, `AllowAnonymous` Aby zezwolić na dostęp nieuwierzytelnionym użytkownikom do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="fa84f-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="fa84f-110">Przykład:</span><span class="sxs-lookup"><span data-stu-id="fa84f-110">For example:</span></span>

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

<span data-ttu-id="fa84f-111">Może to umożliwić tylko uwierzytelnionym użytkownikom `AccountController` , z wyjątkiem `Login` akcji, która jest dostępna dla wszystkich, niezależnie od ich uwierzytelnionego lub nieuwierzytelnionego/anonimowego stanu.</span><span class="sxs-lookup"><span data-stu-id="fa84f-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="fa84f-112">`[AllowAnonymous]`pomija wszystkie instrukcje autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="fa84f-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="fa84f-113">W przypadku łączenia `[AllowAnonymous]` i dowolnych atrybutów `[Authorize]` `[Authorize]` atrybuty zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="fa84f-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="fa84f-114">Na przykład w przypadku zastosowania `[AllowAnonymous]` na poziomie kontrolera wszystkie `[Authorize]` atrybuty na tym samym kontrolerze (lub na dowolnej akcji w ramach tego elementu) zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="fa84f-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
