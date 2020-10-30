---
title: Uwierzytelnianie wieloskładnikowe w ASP.NET Core
author: damienbod
description: Dowiedz się, jak skonfigurować uwierzytelnianie wieloskładnikowe (MFA) w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060394"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="ef9dd-103">Uwierzytelnianie wieloskładnikowe w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef9dd-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="ef9dd-104">Autor [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="ef9dd-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="ef9dd-105">Wyświetlanie lub Pobieranie przykładowego kodu (damienbod/AspNetCoreHybridFlowWithApi repozytorium GitHub)</span><span class="sxs-lookup"><span data-stu-id="ef9dd-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="ef9dd-106">Uwierzytelnianie wieloskładnikowe (MFA) to proces, w którym użytkownik jest proszony podczas logowania w celu uzyskania dodatkowych form identyfikacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="ef9dd-107">Ten monit może być wprowadzeniem kodu z Cellphone, użycie klucza FIDO2 lub w celu przeskanowania odcisku palca.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="ef9dd-108">Gdy wymagana jest druga forma uwierzytelniania, zabezpieczenia są rozszerzane.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="ef9dd-109">Dodatkowy czynnik nie jest łatwo uzyskiwany ani duplikowany przez osobę atakującą.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="ef9dd-110">W tym artykule opisano następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-110">This article covers the following areas:</span></span>

* <span data-ttu-id="ef9dd-111">Co to jest MFA i jakie są zalecane przepływy uwierzytelniania MFA</span><span class="sxs-lookup"><span data-stu-id="ef9dd-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="ef9dd-112">Konfigurowanie uwierzytelniania wieloskładnikowego dla stron administracyjnych przy użyciu :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="ef9dd-112">Configure MFA for administration pages using :::no-loc(ASP.NET Core Identity):::</span></span>
* <span data-ttu-id="ef9dd-113">Wyślij wymaganie logowania MFA do serwera OpenID Connect Connect</span><span class="sxs-lookup"><span data-stu-id="ef9dd-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="ef9dd-114">Wymuś ASP.NET Core OpenID Connect Połącz klienta, aby wymagać uwierzytelniania wieloskładnikowego</span><span class="sxs-lookup"><span data-stu-id="ef9dd-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="ef9dd-115">MFA, FUNKCJI 2FA</span><span class="sxs-lookup"><span data-stu-id="ef9dd-115">MFA, 2FA</span></span>

<span data-ttu-id="ef9dd-116">Uwierzytelnianie wieloskładnikowe wymaga co najmniej dwóch typów dowodu tożsamości, takich jak coś, czego wiadomo, co Ci się podoba, lub sprawdzanie biometryczne użytkownika do uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="ef9dd-117">Uwierzytelnianie dwuskładnikowe (funkcji 2FA) jest podobne do podzestawu MFA, ale różnica polega na tym, że MFA może wymagać co najmniej dwóch czynników, aby potwierdzić tożsamość.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="ef9dd-118">TOTP MFA (algorytm hasła jednorazowego oparty na czasie)</span><span class="sxs-lookup"><span data-stu-id="ef9dd-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="ef9dd-119">Uwierzytelnianie wieloskładnikowe przy użyciu usługi TOTP to obsługiwana implementacja przy użyciu programu :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-119">MFA using TOTP is a supported implementation using :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="ef9dd-120">Może być używany razem z dowolną zgodną aplikacją uwierzytelniania, w tym:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="ef9dd-121">Aplikacja Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="ef9dd-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="ef9dd-122">Aplikacja Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="ef9dd-122">Google Authenticator App</span></span>

<span data-ttu-id="ef9dd-123">Aby uzyskać szczegółowe informacje dotyczące implementacji, zobacz następujący link:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="ef9dd-124">Włącz generowanie kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef9dd-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="ef9dd-125">FIDO2 MFA lub hasła</span><span class="sxs-lookup"><span data-stu-id="ef9dd-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="ef9dd-126">FIDO2 jest obecnie:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="ef9dd-127">Najbezpieczniejszym sposobem osiągnięcia usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="ef9dd-128">Jedyny przepływ usługi MFA, który chroni przed atakami wyłudzania informacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="ef9dd-129">W tej chwili ASP.NET Core nie obsługuje bezpośrednio FIDO2.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="ef9dd-130">FIDO2 można używać w przypadku przepływów MFA lub bezhaseł.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="ef9dd-131">Azure Active Directory zapewnia obsługę przepływów FIDO2 i bezhaseł.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="ef9dd-132">Aby uzyskać więcej informacji, zobacz [Opcje uwierzytelniania bezhasło dla Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="ef9dd-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="ef9dd-133">SMS USŁUGI MFA</span><span class="sxs-lookup"><span data-stu-id="ef9dd-133">MFA SMS</span></span>

<span data-ttu-id="ef9dd-134">Funkcja MFA z programem SMS zwiększa bezpieczeństwo w dużej porównaniu z uwierzytelnianiem haseł (pojedynczym czynnikiem).</span><span class="sxs-lookup"><span data-stu-id="ef9dd-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="ef9dd-135">Jednak korzystanie z wiadomości SMS jako drugiego czynnika nie jest już zalecane.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="ef9dd-136">Istnieje zbyt wiele znanych wektorów ataków dla tego typu implementacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="ef9dd-137">Wskazówki dotyczące NIST</span><span class="sxs-lookup"><span data-stu-id="ef9dd-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="ef9dd-138">Konfigurowanie uwierzytelniania wieloskładnikowego dla stron administracyjnych przy użyciu :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="ef9dd-138">Configure MFA for administration pages using :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="ef9dd-139">Uwierzytelnianie wieloskładnikowe może być wymuszane dla użytkowników w celu uzyskania dostępu do poufnych stron w ramach :::no-loc(ASP.NET Core Identity)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-139">MFA could be forced on users to access sensitive pages within an :::no-loc(ASP.NET Core Identity)::: app.</span></span> <span data-ttu-id="ef9dd-140">Może to być przydatne w przypadku aplikacji, w których istnieją różne poziomy dostępu dla różnych tożsamości.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="ef9dd-141">Na przykład użytkownicy mogą wyświetlać dane profilu przy użyciu hasła logowania, ale do uzyskiwania dostępu do stron administracyjnych będzie wymagane użycie usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="ef9dd-142">Zwiększanie nazwy logowania przy użyciu żądania MFA</span><span class="sxs-lookup"><span data-stu-id="ef9dd-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="ef9dd-143">Kod demonstracyjny jest skonfigurowany przy użyciu ASP.NET Core :::no-loc(Identity)::: ze :::no-loc(Razor)::: stronami i.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-143">The demo code is setup using ASP.NET Core with :::no-loc(Identity)::: and :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="ef9dd-144">`Add:::no-loc(Identity):::`Metoda jest używana zamiast `AddDefault:::no-loc(Identity):::` jednej, więc `IUserClaimsPrincipalFactory` implementacja może zostać użyta do dodania oświadczeń do tożsamości po pomyślnym zalogowaniu.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-144">The `Add:::no-loc(Identity):::` method is used instead of `AddDefault:::no-loc(Identity):::` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User, :::no-loc(Identity):::Role>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<:::no-loc(Identity):::User>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.Add:::no-loc(Razor):::Pages();
}
```

<span data-ttu-id="ef9dd-145">`AdditionalUserClaimsPrincipalFactory`Klasa dodaje `amr` oświadczenie do oświadczeń użytkownika dopiero po pomyślnym zalogowaniu.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="ef9dd-146">Wartość żądania jest odczytywana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-146">The claim's value is read from the database.</span></span> <span data-ttu-id="ef9dd-147">W tym miejscu zostanie dodane zgłoszenie, ponieważ użytkownik powinien uzyskać dostęp do wyższego widoku chronionego tylko wtedy, gdy tożsamość została zarejestrowana za pomocą usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="ef9dd-148">Jeśli widok bazy danych jest odczytywany z bazy danych bezpośrednio, a nie za pomocą tego żądania, można uzyskać dostęp do widoku bez usługi MFA bezpośrednio po aktywowaniu usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace :::no-loc(Identity):::StandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<:::no-loc(Identity):::User, :::no-loc(Identity):::Role>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<:::no-loc(Identity):::User> userManager,
            RoleManager<:::no-loc(Identity):::Role> roleManager, 
            IOptions<:::no-loc(Identity):::Options> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(:::no-loc(Identity):::User user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (Claims:::no-loc(Identity):::)principal.:::no-loc(Identity):::;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="ef9dd-149">Ponieważ :::no-loc(Identity)::: Konfiguracja usługi została zmieniona w `Startup` klasie, należy zaktualizować układy programu :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-149">Because the :::no-loc(Identity)::: service setup changed in the `Startup` class, the layouts of the :::no-loc(Identity)::: need to be updated.</span></span> <span data-ttu-id="ef9dd-150">Szkieletuje :::no-loc(Identity)::: strony w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-150">Scaffold the :::no-loc(Identity)::: pages into the app.</span></span> <span data-ttu-id="ef9dd-151">Zdefiniuj układ w pliku *:::no-loc(Identity)::: /Account/Manage/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-151">Define the layout in the *:::no-loc(Identity):::/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="ef9dd-152">Przypisz również układ dla wszystkich stron zarządzania ze :::no-loc(Identity)::: stron:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-152">Also assign the layout for all the manage pages from the :::no-loc(Identity)::: pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="ef9dd-153">Sprawdzanie wymagań usługi MFA na stronie Administracja</span><span class="sxs-lookup"><span data-stu-id="ef9dd-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="ef9dd-154">Strona Administracja :::no-loc(Razor)::: sprawdza, czy użytkownik zalogował się przy użyciu usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-154">The administration :::no-loc(Razor)::: Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="ef9dd-155">W `OnGet` metodzie tożsamość jest używana w celu uzyskania dostępu do oświadczeń użytkowników.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="ef9dd-156">`amr`Dla tej wartości jest sprawdzana wartość `mfa` .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="ef9dd-157">Jeśli w tożsamości brakuje tego żądania lub jest `false` , przekieruje stronę na stronę Włączanie usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="ef9dd-158">Jest to możliwe, ponieważ użytkownik zalogował się już, ale bez usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-158">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages;

namespace :::no-loc(Identity):::StandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/:::no-loc(Identity):::/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="ef9dd-159">Logika interfejsu użytkownika do przełączania informacji logowania użytkownika</span><span class="sxs-lookup"><span data-stu-id="ef9dd-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="ef9dd-160">Podczas uruchamiania dodano zasady autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="ef9dd-161">Zasady wymagają `amr` roszczeń z wartością `mfa` .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="ef9dd-162">Tych zasad można następnie użyć w widoku, `_Layout` Aby pokazać lub ukryć menu **administratora** z ostrzeżeniem:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.:::no-loc(Identity):::
@inject SignInManager<:::no-loc(Identity):::User> SignInManager
@inject UserManager<:::no-loc(Identity):::User> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="ef9dd-163">Jeśli tożsamość została zarejestrowana przy użyciu usługi MFA, menu **administratora** jest wyświetlane bez ostrzeżenia etykietki narzędzia.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="ef9dd-164">Gdy użytkownik zalogował się bez uwierzytelniania wieloskładnikowego, wyświetlane jest menu **administrator (nie włączono)** wraz z etykietą narzędzia, która informuje użytkownika (wyjaśnienie ostrzeżenia).</span><span class="sxs-lookup"><span data-stu-id="ef9dd-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="ef9dd-165">Jeśli użytkownik zaloguje się bez uwierzytelniania wieloskładnikowego, wyświetlane jest ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-165">If the user logs in without MFA, the warning is displayed:</span></span>

![Uwierzytelnianie MFA administratora](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="ef9dd-167">Użytkownik zostanie przekierowany do widoku włączenia usługi MFA po kliknięciu linku **administratora** :</span><span class="sxs-lookup"><span data-stu-id="ef9dd-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![Administrator aktywuje uwierzytelnianie MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="ef9dd-169">Wyślij wymaganie logowania MFA do serwera OpenID Connect Connect</span><span class="sxs-lookup"><span data-stu-id="ef9dd-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="ef9dd-170">`acr_values`Parametr może służyć do przekazywania `mfa` wymaganej wartości z klienta do serwera w żądaniu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="ef9dd-171">Aby `acr_values` to działało, parametr musi być obsługiwany na serwerze OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="ef9dd-172">OpenID Connect Connect ASP.NET Core Client</span><span class="sxs-lookup"><span data-stu-id="ef9dd-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="ef9dd-173">:::no-loc(Razor):::Aplikacja kliencka OpenID Connect strony ASP.NET Core programu Connect używa `AddOpenIdConnect` metody do logowania się do serwera OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-173">The ASP.NET Core :::no-loc(Razor)::: Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="ef9dd-174">`acr_values`Parametr jest ustawiany z `mfa` wartością i wysyłany z żądaniem uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="ef9dd-175">Służy `OpenIdConnectEvents` do dodawania tego.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="ef9dd-176">Aby uzyskać zalecane `acr_values` wartości parametrów, zobacz [wartości referencyjne metody uwierzytelniania](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="ef9dd-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectTo:::no-loc(Identity):::Provider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="ef9dd-177">Przykład OpenID Connect Connect :::no-loc(Identity)::: Server 4 Server z :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="ef9dd-177">Example OpenID Connect :::no-loc(Identity):::Server 4 server with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="ef9dd-178">Na serwerze OpenID Connect Connect, który jest implementowany przy użyciu :::no-loc(ASP.NET Core Identity)::: programu z widokami MVC, tworzony jest nowy widok o nazwie *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-178">On the OpenID Connect server, which is implemented using :::no-loc(ASP.NET Core Identity)::: with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="ef9dd-179">Widok:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-179">The view:</span></span>

* <span data-ttu-id="ef9dd-180">Wyświetla, jeśli :::no-loc(Identity)::: pochodzi z aplikacji, która wymaga uwierzytelniania wieloskładnikowego, ale nie została aktywowana przez użytkownika w programie :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-180">Displays if the :::no-loc(Identity)::: comes from an app that requires MFA but the user hasn't activated this in :::no-loc(Identity):::.</span></span>
* <span data-ttu-id="ef9dd-181">Informuje użytkownika i dodaje link umożliwiający jego aktywowanie.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-181">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="ef9dd-182">W `Login` metodzie `I:::no-loc(Identity):::ServerInteractionService` Implementacja interfejsu `_interaction` jest używana w celu uzyskania dostępu do parametrów żądania OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-182">In the `Login` method, the `I:::no-loc(Identity):::ServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="ef9dd-183">`acr_values`Do parametru uzyskuje się dostęp przy użyciu `AcrValues` właściwości.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="ef9dd-184">Gdy klient wysłał ten program za pomocą `mfa` zestawu, można to sprawdzić.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="ef9dd-185">Jeśli wymagana jest usługa MFA, a użytkownik w programie :::no-loc(ASP.NET Core Identity)::: ma włączoną usługę MFA, logowanie jest kontynuowane.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-185">If MFA is required, and the user in :::no-loc(ASP.NET Core Identity)::: has MFA enabled, then the login continues.</span></span> <span data-ttu-id="ef9dd-186">Jeśli użytkownik nie ma włączonej usługi MFA, użytkownik zostanie przekierowany do widoku niestandardowego *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="ef9dd-187">Następnie :::no-loc(ASP.NET Core Identity)::: podpisuje użytkownika w programie.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-187">Then :::no-loc(ASP.NET Core Identity)::: signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="ef9dd-188">`ExternalLoginCallback`Metoda działa podobnie jak lokalna :::no-loc(Identity)::: Nazwa logowania.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-188">The `ExternalLoginCallback` method works like the local :::no-loc(Identity)::: login.</span></span> <span data-ttu-id="ef9dd-189">`AcrValues`Właściwość jest sprawdzana pod kątem `mfa` wartości.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="ef9dd-190">Jeśli `mfa` wartość jest obecna, uwierzytelnianie wieloskładnikowe jest wymuszane przed zakończeniem logowania (na przykład przekierowane do `ErrorEnable2FA` widoku).</span><span class="sxs-lookup"><span data-stu-id="ef9dd-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="ef9dd-191">Jeśli użytkownik jest już zalogowany, aplikacja kliencka:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="ef9dd-192">Nadal sprawdza poprawność `amr` .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="ef9dd-193">Usługę MFA można skonfigurować za pomocą linku do :::no-loc(ASP.NET Core Identity)::: widoku.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-193">Can set up the MFA with a link to the :::no-loc(ASP.NET Core Identity)::: view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="ef9dd-195">Wymuś ASP.NET Core OpenID Connect Połącz klienta, aby wymagać uwierzytelniania wieloskładnikowego</span><span class="sxs-lookup"><span data-stu-id="ef9dd-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="ef9dd-196">Ten przykład pokazuje, w jaki sposób :::no-loc(Razor)::: aplikacja ASP.NET Core Page, która używa OpenID Connect Connect do logowania, może wymagać uwierzytelniania użytkowników przy użyciu usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-196">This example shows how an ASP.NET Core :::no-loc(Razor)::: Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="ef9dd-197">Aby sprawdzić wymaganie usługi MFA, `IAuthorizationRequirement` jest tworzone wymaganie.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="ef9dd-198">Ta wartość zostanie dodana do stron przy użyciu zasad, które wymagają uwierzytelniania wieloskładnikowego.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="ef9dd-199">`AuthorizationHandler`Wdrożono, który będzie używać `amr` roszczeń i sprawdzać wartość `mfa` .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="ef9dd-200">`amr`Jest zwracany w przypadku `id_token` pomyślnego uwierzytelnienia i może mieć wiele różnych wartości zgodnie z definicją w specyfikacji [wartości odwołania metody uwierzytelniania](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="ef9dd-201">Zwracana wartość zależy od tego, jak tożsamość została uwierzytelniona i w implementacji serwera OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="ef9dd-202">Program `AuthorizationHandler` używa `RequireMfa` wymagania i sprawdza poprawność tego `amr` żądania.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="ef9dd-203">Serwer OpenID Connect Connect można zaimplementować przy użyciu :::no-loc(Identity)::: Server4 z :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-203">The OpenID Connect server can be implemented using :::no-loc(Identity):::Server4 with :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="ef9dd-204">Gdy użytkownik loguje się przy użyciu TOTP, `amr` jest zwracana wartość usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="ef9dd-205">Jeśli jest używana inna implementacja serwera OpenID Connect Connect lub inny typ usługi MFA, w ramach tego `amr` żądania lub może być używana inna wartość.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="ef9dd-206">Kod musi być rozszerzony, aby można było go również zaakceptować.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-206">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="ef9dd-207">W `Startup.ConfigureServices` metodzie `AddOpenIdConnect` Metoda jest używana jako domyślny schemat wyzwania.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="ef9dd-208">Procedura obsługi autoryzacji, która jest używana do sprawdzania `amr` roszczeń, jest dodawana do kontenera kontroli Inversion.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="ef9dd-209">Następnie tworzone są zasady, które dodaje `RequireMfa` wymaganie.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplication:::no-loc(Cookie):::(options =>
        options.:::no-loc(Cookie):::.SecurePolicy =
            :::no-loc(Cookie):::SecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.Add:::no-loc(Razor):::Pages();
}
```

<span data-ttu-id="ef9dd-210">Te zasady są następnie używane na :::no-loc(Razor)::: stronie zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-210">This policy is then used in the :::no-loc(Razor)::: page as required.</span></span> <span data-ttu-id="ef9dd-211">Zasady mogą być również dodawane globalnie dla całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-211">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="ef9dd-212">Jeśli użytkownik uwierzytelnia się bez uwierzytelniania wieloskładnikowego, `amr` będzie prawdopodobnie miał `pwd` wartość.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="ef9dd-213">Żądanie nie zostanie autoryzowane do uzyskania dostępu do strony.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="ef9dd-214">Przy użyciu wartości domyślnych użytkownik zostanie przekierowany na stronę *Account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="ef9dd-215">Takie zachowanie można zmienić lub wdrożyć własną logikę niestandardową tutaj.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="ef9dd-216">W tym przykładzie zostanie dodany link, dzięki czemu prawidłowy użytkownik może skonfigurować uwierzytelnianie wieloskładnikowe dla swojego konta.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="ef9dd-217">Teraz tylko użytkownicy, którzy uwierzytelniają się za pomocą usługi MFA, mogą uzyskiwać dostęp do strony lub witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="ef9dd-218">Jeśli używane są różne typy MFA lub jeśli funkcji 2FA jest poprawny, to w przypadku wystąpienia tego `amr` żądania będą mieć różne wartości i muszą zostać prawidłowo przetworzone.</span><span class="sxs-lookup"><span data-stu-id="ef9dd-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="ef9dd-219">Różne serwery OpenID Connect Connect również zwracają różne wartości dla tego żądania i mogą nie być zgodne ze specyfikacją [wartości odwołania metody uwierzytelniania](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="ef9dd-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="ef9dd-220">Logowanie bez uwierzytelniania wieloskładnikowego (na przykład przy użyciu hasła):</span><span class="sxs-lookup"><span data-stu-id="ef9dd-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="ef9dd-221">`amr`Ma `pwd` wartość:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="ef9dd-223">Odmowa dostępu:</span><span class="sxs-lookup"><span data-stu-id="ef9dd-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="ef9dd-225">Alternatywnie, logowanie przy użyciu uwierzytelniania OTP z :::no-loc(Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="ef9dd-225">Alternatively, logging in using OTP with :::no-loc(Identity)::::</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="ef9dd-227">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ef9dd-227">Additional resources</span></span>

* [<span data-ttu-id="ef9dd-228">Włącz generowanie kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef9dd-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="ef9dd-229">Opcje uwierzytelniania bezhasło dla Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="ef9dd-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="ef9dd-230">FIDO2 .NET Library for FIDO2/WebAuthn zaświadczanie i potwierdzenie przy użyciu platformy .NET</span><span class="sxs-lookup"><span data-stu-id="ef9dd-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="ef9dd-231">WebAuthn awesome</span><span class="sxs-lookup"><span data-stu-id="ef9dd-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
