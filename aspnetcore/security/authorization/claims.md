---
title: Autoryzacja oparta na oświadczeniach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać sprawdzanie oświadczeń pod kątem autoryzacji w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/claims
ms.openlocfilehash: 639cacbab2688adfe769ef2c6954ea877cf0c66a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022358"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autoryzacja oparta na oświadczeniach w ASP.NET Core

<a name="security-authorization-claims-based"></a>

Po utworzeniu tożsamości może zostać przypisane jedno lub więcej oświadczeń wystawionych przez zaufaną stronę. Jest to para wartości Nazwa, która reprezentuje temat, a nie co może zrobić. Na przykład użytkownik może mieć licencję sterownika wydaną przez Urząd lokalnej licencji na kierowanie. Licencja sterownika ma swoją datę urodzenia. W takim przypadku nazwa żądania powinna być równa `DateOfBirth` dacie urodzenia, na przykład, `8th June 1970` a wystawca będzie urzędem licencjonowania. Autoryzacja oparta na oświadczeniach, w najprostszy sposób, sprawdza wartość oświadczenia i zezwala na dostęp do zasobu na podstawie tej wartości. Na przykład jeśli chcesz uzyskać dostęp do klubu nocnego, proces autoryzacji może być:

Specjalista ds. zabezpieczeń analizuje wartość daty wystąpienia urodzenia i czy ufa wystawcy (urząd certyfikacji kierowania) przed udzieleniem dostępu.

Tożsamość może zawierać wiele oświadczeń z wieloma wartościami i może zawierać wiele oświadczeń tego samego typu.

## <a name="adding-claims-checks"></a>Dodawanie sprawdzania oświadczeń

Kontrola autoryzacji oparta na oświadczeniach jest deklaratywna — deweloperzy są osadzani w kodzie, względem kontrolera lub akcji w ramach kontrolera, określając oświadczenia, których bieżący użytkownik musi posiadać, i opcjonalnie wartość, którą oświadczenie musi przechowywać, aby uzyskać dostęp do żądanego zasobu. Wymagania dotyczące oświadczeń są oparte na zasadach, Deweloper musi skompilować i zarejestrować zasady wyrażające wymagania dotyczące oświadczeń.

Najprostszy typ zasad dotyczących roszczeń szuka obecności roszczeń i nie sprawdza wartości.

Najpierw należy skompilować i zarejestrować zasady. Odbywa się to w ramach konfiguracji usługi autoryzacji, która zwykle uczestniczy w `ConfigureServices()` pliku *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

W takim przypadku `EmployeeOnly` zasady sprawdzają obecność `EmployeeNumber` roszczeń w bieżącej tożsamości.

Następnie należy zastosować zasady przy użyciu `Policy` właściwości w atrybucie, `AuthorizeAttribute` Aby określić nazwę zasad;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

Ten `AuthorizeAttribute` atrybut może być stosowany do całego kontrolera, w tym wystąpieniu tylko tożsamości pasujące do zasad będą mieć dostęp do dowolnej akcji na kontrolerze.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Jeśli masz kontroler, który jest chroniony przez `AuthorizeAttribute` atrybut, ale chcesz zezwolić na dostęp anonimowy do określonych akcji, należy zastosować `AllowAnonymousAttribute` atrybut.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

Większość oświadczeń ma wartość. Podczas tworzenia zasad można określić listę dozwolonych wartości. Poniższy przykład powiedzie się tylko dla pracowników, których numer pracownika to 1, 2, 3, 4 lub 5.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Dodawanie ogólnego sprawdzania roszczeń

Jeśli wartość oświadczenia nie jest pojedynczą wartością lub wymagana jest transformacja, użyj [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Aby uzyskać więcej informacji, zobacz [Używanie funkcji Func do realizacji zasad](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Obliczanie wielu zasad

W przypadku zastosowania wielu zasad do kontrolera lub akcji wszystkie zasady muszą zostać przekazane przed udzieleniem dostępu. Przykład:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

W powyższym przykładzie Każda tożsamość, która spełnia `EmployeeOnly` zasady, może uzyskać dostęp do `Payslip` akcji, ponieważ te zasady są wymuszane na kontrolerze. Jednak w celu wywołania `UpdateSalary` akcji tożsamość musi spełniać *zarówno* `EmployeeOnly` zasady, jak i `HumanResources` zasady.

Jeśli potrzebujesz bardziej skomplikowanych zasad, takich jak pobieranie daty wystąpienia urodzenia, obliczanie wieku od IT, sprawdzenie wieku wynosi 21 lub starsze, należy napisać [niestandardowe programy obsługi zasad](xref:security/authorization/policies).
