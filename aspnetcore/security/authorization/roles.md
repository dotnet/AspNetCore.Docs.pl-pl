---
title: Autoryzacja oparta na rolach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ograniczyć dostęp do kontrolera ASP.NET Core i akcji, przekazując role do atrybutu Autoryzuj.
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051164"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Autoryzacja oparta na rolach w ASP.NET Core

<a name="security-authorization-role-based"></a>

Po utworzeniu tożsamości może ona należeć do co najmniej jednej roli. Na przykład Tracy może należeć do ról administratora i użytkownika, gdy Scott może należeć tylko do roli użytkownika. Sposób tworzenia i zarządzania tymi rolami zależy od magazynu zapasowego procesu autoryzacji. Role są udostępniane deweloperowi za pomocą metody [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) klasy [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Dodawanie kontroli roli

Kontrola autoryzacji oparta na rolach polega &mdash; na tym, że deweloperzy są osadzani w kodzie, względem kontrolera lub akcji w ramach kontrolera, określając role, do których bieżący użytkownik musi być członkiem, aby uzyskać dostęp do żądanego zasobu.

Na przykład poniższy kod ogranicza dostęp do wszystkich akcji w odniesieniu `AdministrationController` do użytkowników, którzy są członkami `Administrator` roli:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Można określić wiele ról jako listę rozdzielonych przecinkami:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Ten kontroler będzie dostępny tylko dla użytkowników, którzy są członkami `HRManager` roli lub `Finance` roli.

Jeśli zastosujesz wiele atrybutów, użytkownik uzyskujący dostęp musi być członkiem wszystkich określonych ról; Poniższy przykład wymaga, aby użytkownik musiał być członkiem `PowerUser` `ControlPanelUser` roli i.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Można dodatkowo ograniczyć dostęp, stosując dodatkowe atrybuty autoryzacji roli na poziomie akcji:

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

W poprzednim fragmencie kodu członkowie `Administrator` roli lub `PowerUser` roli mogą uzyskać dostęp do kontrolera i `SetTime` akcji, ale tylko członkowie `Administrator` roli mogą uzyskać do niej dostęp `ShutDown` .

Możesz również zablokować kontroler, ale zezwolić na anonimowy, nieuwierzytelniony dostęp do poszczególnych akcji.

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

W przypadku Razor stron `AuthorizeAttribute` można zastosować jedną z nich:

* Przy użyciu [Konwencji](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)lub
* Zastosowanie `AuthorizeAttribute` do `PageModel` wystąpienia:

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
> Atrybuty filtru, w tym `AuthorizeAttribute` , mogą być stosowane tylko do PageModel i nie mogą być stosowane do określonych metod obsługi stron.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Kontrola ról oparta na zasadach

Wymagania dotyczące ról można również wyrazić przy użyciu nowej składni zasad, w której deweloper rejestruje zasady podczas uruchamiania w ramach konfiguracji usługi autoryzacji. Zwykle jest to wykonywane w `ConfigureServices()` pliku *Startup.cs* .

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

Zasady są stosowane przy użyciu `Policy` właściwości w `AuthorizeAttribute` atrybucie:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Jeśli chcesz określić wiele dozwolonych ról w wymaganiu, możesz je określić jako parametry `RequireRole` metody:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Ten przykład autoryzuje użytkowników, którzy należą do `Administrator` `PowerUser` `BackupAdministrator` ról.

### <a name="add-role-services-to-no-locidentity"></a>Dodawanie usług ról do programu Identity

Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

