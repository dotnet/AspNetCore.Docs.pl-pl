---
title: Autoryzacja oparta na rolach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ograniczyć dostęp do kontrolera ASP.NET Core i akcji, przekazując role do atrybutu Autoryzuj.
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
uid: security/authorization/roles
ms.openlocfilehash: 5d2ea6b9be0c993d62fa75fb8b471b5923747bac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407866"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="9e80c-103">Autoryzacja oparta na rolach w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e80c-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="9e80c-104">Po utworzeniu tożsamości może ona należeć do co najmniej jednej roli.</span><span class="sxs-lookup"><span data-stu-id="9e80c-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="9e80c-105">Na przykład Tracy może należeć do ról administratora i użytkownika, gdy Scott może należeć tylko do roli użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9e80c-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="9e80c-106">Sposób tworzenia i zarządzania tymi rolami zależy od magazynu zapasowego procesu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9e80c-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="9e80c-107">Role są udostępniane deweloperowi za pomocą metody [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) klasy [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="9e80c-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="9e80c-108">Dodawanie kontroli roli</span><span class="sxs-lookup"><span data-stu-id="9e80c-108">Adding role checks</span></span>

<span data-ttu-id="9e80c-109">Kontrola autoryzacji oparta na rolach polega &mdash; na tym, że deweloperzy są osadzani w kodzie, względem kontrolera lub akcji w ramach kontrolera, określając role, do których bieżący użytkownik musi być członkiem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="9e80c-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="9e80c-110">Na przykład poniższy kod ogranicza dostęp do wszystkich akcji w odniesieniu `AdministrationController` do użytkowników, którzy są członkami `Administrator` roli:</span><span class="sxs-lookup"><span data-stu-id="9e80c-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="9e80c-111">Można określić wiele ról jako listę rozdzielonych przecinkami:</span><span class="sxs-lookup"><span data-stu-id="9e80c-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="9e80c-112">Ten kontroler będzie dostępny tylko dla użytkowników, którzy są członkami `HRManager` roli lub `Finance` roli.</span><span class="sxs-lookup"><span data-stu-id="9e80c-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="9e80c-113">Jeśli zastosujesz wiele atrybutów, użytkownik uzyskujący dostęp musi być członkiem wszystkich określonych ról; Poniższy przykład wymaga, aby użytkownik musiał być członkiem `PowerUser` `ControlPanelUser` roli i.</span><span class="sxs-lookup"><span data-stu-id="9e80c-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="9e80c-114">Można dodatkowo ograniczyć dostęp, stosując dodatkowe atrybuty autoryzacji roli na poziomie akcji:</span><span class="sxs-lookup"><span data-stu-id="9e80c-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="9e80c-115">W poprzednim fragmencie kodu członkowie `Administrator` roli lub `PowerUser` roli mogą uzyskać dostęp do kontrolera i `SetTime` akcji, ale tylko członkowie `Administrator` roli mogą uzyskać do niej dostęp `ShutDown` .</span><span class="sxs-lookup"><span data-stu-id="9e80c-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="9e80c-116">Możesz również zablokować kontroler, ale zezwolić na anonimowy, nieuwierzytelniony dostęp do poszczególnych akcji.</span><span class="sxs-lookup"><span data-stu-id="9e80c-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="9e80c-117">W przypadku Razor stron `AuthorizeAttribute` można zastosować jedną z nich:</span><span class="sxs-lookup"><span data-stu-id="9e80c-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="9e80c-118">Przy użyciu [Konwencji](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)lub</span><span class="sxs-lookup"><span data-stu-id="9e80c-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="9e80c-119">Zastosowanie `AuthorizeAttribute` do `PageModel` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="9e80c-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="9e80c-120">Atrybuty filtru, w tym `AuthorizeAttribute` , mogą być stosowane tylko do PageModel i nie mogą być stosowane do określonych metod obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="9e80c-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="9e80c-121">Kontrola ról oparta na zasadach</span><span class="sxs-lookup"><span data-stu-id="9e80c-121">Policy based role checks</span></span>

<span data-ttu-id="9e80c-122">Wymagania dotyczące ról można również wyrazić przy użyciu nowej składni zasad, w której deweloper rejestruje zasady podczas uruchamiania w ramach konfiguracji usługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9e80c-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="9e80c-123">Zwykle jest to wykonywane w `ConfigureServices()` pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="9e80c-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

<span data-ttu-id="9e80c-124">Zasady są stosowane przy użyciu `Policy` właściwości w `AuthorizeAttribute` atrybucie:</span><span class="sxs-lookup"><span data-stu-id="9e80c-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="9e80c-125">Jeśli chcesz określić wiele dozwolonych ról w wymaganiu, możesz je określić jako parametry `RequireRole` metody:</span><span class="sxs-lookup"><span data-stu-id="9e80c-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="9e80c-126">Ten przykład autoryzuje użytkowników, którzy należą do `Administrator` `PowerUser` `BackupAdministrator` ról.</span><span class="sxs-lookup"><span data-stu-id="9e80c-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="9e80c-127">Dodawanie usług ról do programuIdentity</span><span class="sxs-lookup"><span data-stu-id="9e80c-127">Add Role services to Identity</span></span>

<span data-ttu-id="9e80c-128">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="9e80c-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

