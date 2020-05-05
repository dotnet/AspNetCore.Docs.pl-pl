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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="4fcfd-103">Prosta autoryzacja w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4fcfd-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="4fcfd-104">Autoryzacja w MVC jest kontrolowana przez `AuthorizeAttribute` atrybut i jego różne parametry.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="4fcfd-105">W najprostszej sposób stosowanie `AuthorizeAttribute` atrybutu do kontrolera lub akcji ogranicza dostęp do kontrolera lub akcji do dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="4fcfd-106">Na przykład poniższy kod ogranicza dostęp do `AccountController` dowolnego uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="4fcfd-107">Jeśli chcesz zastosować autoryzację do akcji, a nie kontrolera, Zastosuj `AuthorizeAttribute` atrybut do samej akcji:</span><span class="sxs-lookup"><span data-stu-id="4fcfd-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="4fcfd-108">Teraz tylko uwierzytelnieni użytkownicy mogą uzyskiwać dostęp `Logout` do funkcji.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="4fcfd-109">Można również użyć atrybutu, `AllowAnonymous` aby zezwolić na dostęp nieuwierzytelnionym użytkownikom do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="4fcfd-110">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4fcfd-110">For example:</span></span>

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

<span data-ttu-id="4fcfd-111">Może to umożliwić tylko uwierzytelnionym użytkownikom `AccountController`, z wyjątkiem `Login` akcji, która jest dostępna dla wszystkich, niezależnie od ich uwierzytelnionego lub nieuwierzytelnionego/anonimowego stanu.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="4fcfd-112">`[AllowAnonymous]`pomija wszystkie instrukcje autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="4fcfd-113">W `[Authorize]` przypadku łączenia `[AllowAnonymous]` i dowolnych `[Authorize]` atrybutów atrybuty zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="4fcfd-114">Na przykład w przypadku zastosowania `[AllowAnonymous]` na poziomie kontrolera wszystkie `[Authorize]` atrybuty na tym samym kontrolerze (lub na dowolnej akcji w ramach tego elementu) zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="4fcfd-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
