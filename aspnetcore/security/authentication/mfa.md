---
title: Uwierzytelnianie wieloskładnikowe w ASP.NET Core
author: damienbod
description: Dowiedz się, jak skonfigurować uwierzytelnianie wieloskładnikowe (MFA) w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
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
uid: security/authentication/mfa
ms.openlocfilehash: 4538030b4ce6aba6c78edb69cf44fc5812ddff76
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017860"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="5eb44-103">Uwierzytelnianie wieloskładnikowe w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb44-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="5eb44-104">Autor [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="5eb44-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

<span data-ttu-id="5eb44-105">Uwierzytelnianie wieloskładnikowe (MFA) to proces, w którym użytkownik jest proszony podczas logowania w celu uzyskania dodatkowych form identyfikacji.</span><span class="sxs-lookup"><span data-stu-id="5eb44-105">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="5eb44-106">Ten monit może być wprowadzeniem kodu z Cellphone, użycie klucza FIDO2 lub w celu przeskanowania odcisku palca.</span><span class="sxs-lookup"><span data-stu-id="5eb44-106">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="5eb44-107">Gdy wymagana jest druga forma uwierzytelniania, zabezpieczenia są rozszerzane.</span><span class="sxs-lookup"><span data-stu-id="5eb44-107">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="5eb44-108">Dodatkowy czynnik nie jest łatwo uzyskiwany ani duplikowany przez osobę atakującą.</span><span class="sxs-lookup"><span data-stu-id="5eb44-108">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="5eb44-109">W tym artykule opisano następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="5eb44-109">This article covers the following areas:</span></span>

* <span data-ttu-id="5eb44-110">Co to jest MFA i jakie są zalecane przepływy uwierzytelniania MFA</span><span class="sxs-lookup"><span data-stu-id="5eb44-110">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="5eb44-111">Konfigurowanie uwierzytelniania wieloskładnikowego dla stron administracyjnych przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="5eb44-111">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="5eb44-112">Wyślij wymaganie logowania MFA do serwera OpenID Connect Connect</span><span class="sxs-lookup"><span data-stu-id="5eb44-112">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="5eb44-113">Wymuś ASP.NET Core OpenID Connect Połącz klienta, aby wymagać uwierzytelniania wieloskładnikowego</span><span class="sxs-lookup"><span data-stu-id="5eb44-113">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="5eb44-114">MFA, FUNKCJI 2FA</span><span class="sxs-lookup"><span data-stu-id="5eb44-114">MFA, 2FA</span></span>

<span data-ttu-id="5eb44-115">Uwierzytelnianie wieloskładnikowe wymaga co najmniej dwóch typów dowodu tożsamości, takich jak coś, czego wiadomo, co Ci się podoba, lub sprawdzanie biometryczne użytkownika do uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="5eb44-115">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="5eb44-116">Uwierzytelnianie dwuskładnikowe (funkcji 2FA) jest podobne do podzestawu MFA, ale różnica polega na tym, że MFA może wymagać co najmniej dwóch czynników, aby potwierdzić tożsamość.</span><span class="sxs-lookup"><span data-stu-id="5eb44-116">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="5eb44-117">TOTP MFA (algorytm hasła jednorazowego oparty na czasie)</span><span class="sxs-lookup"><span data-stu-id="5eb44-117">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="5eb44-118">Uwierzytelnianie wieloskładnikowe przy użyciu usługi TOTP to obsługiwana implementacja przy użyciu ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5eb44-118">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="5eb44-119">Może być używany razem z dowolną zgodną aplikacją uwierzytelniania, w tym:</span><span class="sxs-lookup"><span data-stu-id="5eb44-119">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="5eb44-120">Aplikacja Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="5eb44-120">Microsoft Authenticator App</span></span>
* <span data-ttu-id="5eb44-121">Aplikacja Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="5eb44-121">Google Authenticator App</span></span>

<span data-ttu-id="5eb44-122">Aby uzyskać szczegółowe informacje dotyczące implementacji, zobacz następujący link:</span><span class="sxs-lookup"><span data-stu-id="5eb44-122">See the following link for implementation details:</span></span>

[<span data-ttu-id="5eb44-123">Włącz generowanie kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb44-123">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="5eb44-124">FIDO2 MFA lub hasła</span><span class="sxs-lookup"><span data-stu-id="5eb44-124">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="5eb44-125">FIDO2 jest obecnie:</span><span class="sxs-lookup"><span data-stu-id="5eb44-125">FIDO2 is currently:</span></span>

* <span data-ttu-id="5eb44-126">Najbezpieczniejszym sposobem osiągnięcia usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-126">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="5eb44-127">Jedyny przepływ usługi MFA, który chroni przed atakami wyłudzania informacji.</span><span class="sxs-lookup"><span data-stu-id="5eb44-127">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="5eb44-128">W tej chwili ASP.NET Core nie obsługuje bezpośrednio FIDO2.</span><span class="sxs-lookup"><span data-stu-id="5eb44-128">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="5eb44-129">FIDO2 można używać w przypadku przepływów MFA lub bezhaseł.</span><span class="sxs-lookup"><span data-stu-id="5eb44-129">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="5eb44-130">Azure Active Directory zapewnia obsługę przepływów FIDO2 i bezhaseł.</span><span class="sxs-lookup"><span data-stu-id="5eb44-130">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="5eb44-131">Aby uzyskać więcej informacji, zobacz [Opcje uwierzytelniania bezhasło dla Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="5eb44-131">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="5eb44-132">SMS USŁUGI MFA</span><span class="sxs-lookup"><span data-stu-id="5eb44-132">MFA SMS</span></span>

<span data-ttu-id="5eb44-133">Funkcja MFA z programem SMS zwiększa bezpieczeństwo w dużej porównaniu z uwierzytelnianiem haseł (pojedynczym czynnikiem).</span><span class="sxs-lookup"><span data-stu-id="5eb44-133">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="5eb44-134">Jednak korzystanie z wiadomości SMS jako drugiego czynnika nie jest już zalecane.</span><span class="sxs-lookup"><span data-stu-id="5eb44-134">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="5eb44-135">Istnieje zbyt wiele znanych wektorów ataków dla tego typu implementacji.</span><span class="sxs-lookup"><span data-stu-id="5eb44-135">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="5eb44-136">Wskazówki dotyczące NIST</span><span class="sxs-lookup"><span data-stu-id="5eb44-136">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-no-locidentity"></a><span data-ttu-id="5eb44-137">Konfigurowanie uwierzytelniania wieloskładnikowego dla stron administracyjnych przy użyciu ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="5eb44-137">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="5eb44-138">Uwierzytelnianie wieloskładnikowe może być wymuszane dla użytkowników w celu uzyskania dostępu do poufnych stron w ramach Identity aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5eb44-138">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="5eb44-139">Może to być przydatne w przypadku aplikacji, w których istnieją różne poziomy dostępu dla różnych tożsamości.</span><span class="sxs-lookup"><span data-stu-id="5eb44-139">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="5eb44-140">Na przykład użytkownicy mogą wyświetlać dane profilu przy użyciu hasła logowania, ale do uzyskiwania dostępu do stron administracyjnych będzie wymagane użycie usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-140">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="5eb44-141">Zwiększanie nazwy logowania przy użyciu żądania MFA</span><span class="sxs-lookup"><span data-stu-id="5eb44-141">Extend the login with an MFA claim</span></span>

<span data-ttu-id="5eb44-142">Kod demonstracyjny jest skonfigurowany przy użyciu ASP.NET Core Identity ze Razor stronami i.</span><span class="sxs-lookup"><span data-stu-id="5eb44-142">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="5eb44-143">`AddIdentity`Metoda jest używana zamiast `AddDefaultIdentity` jednej, więc `IUserClaimsPrincipalFactory` implementacja może zostać użyta do dodania oświadczeń do tożsamości po pomyślnym zalogowaniu.</span><span class="sxs-lookup"><span data-stu-id="5eb44-143">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="5eb44-144">`AdditionalUserClaimsPrincipalFactory`Klasa dodaje `amr` oświadczenie do oświadczeń użytkownika dopiero po pomyślnym zalogowaniu.</span><span class="sxs-lookup"><span data-stu-id="5eb44-144">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="5eb44-145">Wartość żądania jest odczytywana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5eb44-145">The claim's value is read from the database.</span></span> <span data-ttu-id="5eb44-146">W tym miejscu zostanie dodane zgłoszenie, ponieważ użytkownik powinien uzyskać dostęp do wyższego widoku chronionego tylko wtedy, gdy tożsamość została zarejestrowana za pomocą usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-146">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="5eb44-147">Jeśli widok bazy danych jest odczytywany z bazy danych bezpośrednio, a nie za pomocą tego żądania, można uzyskać dostęp do widoku bez usługi MFA bezpośrednio po aktywowaniu usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-147">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

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

<span data-ttu-id="5eb44-148">Ponieważ Identity Konfiguracja usługi została zmieniona w `Startup` klasie, należy zaktualizować układy programu Identity .</span><span class="sxs-lookup"><span data-stu-id="5eb44-148">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="5eb44-149">Szkieletuje Identity strony w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5eb44-149">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="5eb44-150">Zdefiniuj układ w pliku \* Identity /Account/Manage/_Layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="5eb44-150">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="5eb44-151">Przypisz również układ dla wszystkich stron zarządzania ze Identity stron:</span><span class="sxs-lookup"><span data-stu-id="5eb44-151">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="5eb44-152">Sprawdzanie wymagań usługi MFA na stronie Administracja</span><span class="sxs-lookup"><span data-stu-id="5eb44-152">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="5eb44-153">Strona Administracja Razor sprawdza, czy użytkownik zalogował się przy użyciu usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-153">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="5eb44-154">W `OnGet` metodzie tożsamość jest używana w celu uzyskania dostępu do oświadczeń użytkowników.</span><span class="sxs-lookup"><span data-stu-id="5eb44-154">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="5eb44-155">`amr`Dla tej wartości jest sprawdzana wartość `mfa` .</span><span class="sxs-lookup"><span data-stu-id="5eb44-155">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="5eb44-156">Jeśli w tożsamości brakuje tego żądania lub jest `false` , przekieruje stronę na stronę Włączanie usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-156">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="5eb44-157">Jest to możliwe, ponieważ użytkownik zalogował się już, ale bez usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-157">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
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
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="5eb44-158">Logika interfejsu użytkownika do przełączania informacji logowania użytkownika</span><span class="sxs-lookup"><span data-stu-id="5eb44-158">UI logic to toggle user login information</span></span>

<span data-ttu-id="5eb44-159">Podczas uruchamiania dodano zasady autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="5eb44-159">An authorization policy was added at startup.</span></span> <span data-ttu-id="5eb44-160">Zasady wymagają `amr` roszczeń z wartością `mfa` .</span><span class="sxs-lookup"><span data-stu-id="5eb44-160">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="5eb44-161">Tych zasad można następnie użyć w widoku, `_Layout` Aby pokazać lub ukryć menu **administratora** z ostrzeżeniem:</span><span class="sxs-lookup"><span data-stu-id="5eb44-161">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="5eb44-162">Jeśli tożsamość została zarejestrowana przy użyciu usługi MFA, menu **administratora** jest wyświetlane bez ostrzeżenia etykietki narzędzia.</span><span class="sxs-lookup"><span data-stu-id="5eb44-162">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="5eb44-163">Gdy użytkownik zalogował się bez uwierzytelniania wieloskładnikowego, wyświetlane jest menu **administrator (nie włączono)** wraz z etykietą narzędzia, która informuje użytkownika (wyjaśnienie ostrzeżenia).</span><span class="sxs-lookup"><span data-stu-id="5eb44-163">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="5eb44-164">Jeśli użytkownik zaloguje się bez uwierzytelniania wieloskładnikowego, wyświetlane jest ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="5eb44-164">If the user logs in without MFA, the warning is displayed:</span></span>

![Uwierzytelnianie MFA administratora](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="5eb44-166">Użytkownik zostanie przekierowany do widoku włączenia usługi MFA po kliknięciu linku **administratora** :</span><span class="sxs-lookup"><span data-stu-id="5eb44-166">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![Administrator aktywuje uwierzytelnianie MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="5eb44-168">Wyślij wymaganie logowania MFA do serwera OpenID Connect Connect</span><span class="sxs-lookup"><span data-stu-id="5eb44-168">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="5eb44-169">`acr_values`Parametr może służyć do przekazywania `mfa` wymaganej wartości z klienta do serwera w żądaniu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="5eb44-169">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="5eb44-170">Aby `acr_values` to działało, parametr musi być obsługiwany na serwerze OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="5eb44-170">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="5eb44-171">OpenID Connect Connect ASP.NET Core Client</span><span class="sxs-lookup"><span data-stu-id="5eb44-171">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="5eb44-172">RazorAplikacja kliencka OpenID Connect strony ASP.NET Core programu Connect używa `AddOpenIdConnect` metody do logowania się do serwera OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="5eb44-172">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="5eb44-173">`acr_values`Parametr jest ustawiany z `mfa` wartością i wysyłany z żądaniem uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="5eb44-173">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="5eb44-174">Służy `OpenIdConnectEvents` do dodawania tego.</span><span class="sxs-lookup"><span data-stu-id="5eb44-174">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="5eb44-175">Aby uzyskać zalecane `acr_values` wartości parametrów, zobacz [wartości referencyjne metody uwierzytelniania](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="5eb44-175">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
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
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-aspnet-core-no-locidentity"></a><span data-ttu-id="5eb44-176">Przykład OpenID Connect Connect Identity Server 4 Server z ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="5eb44-176">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="5eb44-177">Na serwerze OpenID Connect Connect, który jest implementowany przy użyciu ASP.NET Core Identity z widokami MVC, tworzony jest nowy widok o nazwie *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5eb44-177">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="5eb44-178">Widok:</span><span class="sxs-lookup"><span data-stu-id="5eb44-178">The view:</span></span>

* <span data-ttu-id="5eb44-179">Wyświetla, jeśli Identity pochodzi z aplikacji, która wymaga uwierzytelniania wieloskładnikowego, ale nie została aktywowana przez użytkownika w programie Identity .</span><span class="sxs-lookup"><span data-stu-id="5eb44-179">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="5eb44-180">Informuje użytkownika i dodaje link umożliwiający jego aktywowanie.</span><span class="sxs-lookup"><span data-stu-id="5eb44-180">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="5eb44-181">W `Login` metodzie `IIdentityServerInteractionService` Implementacja interfejsu `_interaction` jest używana w celu uzyskania dostępu do parametrów żądania OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="5eb44-181">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="5eb44-182">`acr_values`Do parametru uzyskuje się dostęp przy użyciu `AcrValues` właściwości.</span><span class="sxs-lookup"><span data-stu-id="5eb44-182">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="5eb44-183">Gdy klient wysłał ten program za pomocą `mfa` zestawu, można to sprawdzić.</span><span class="sxs-lookup"><span data-stu-id="5eb44-183">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="5eb44-184">Jeśli wymagana jest usługa MFA, a użytkownik w ASP.NET Core Identity ma włączoną usługę MFA, logowanie będzie kontynuowane.</span><span class="sxs-lookup"><span data-stu-id="5eb44-184">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="5eb44-185">Jeśli użytkownik nie ma włączonej usługi MFA, użytkownik zostanie przekierowany do widoku niestandardowego *ErrorEnable2FA. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5eb44-185">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="5eb44-186">Następnie ASP.NET Core Identity podpisać użytkownika w programie.</span><span class="sxs-lookup"><span data-stu-id="5eb44-186">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="5eb44-187">`ExternalLoginCallback`Metoda działa podobnie jak lokalna Identity Nazwa logowania.</span><span class="sxs-lookup"><span data-stu-id="5eb44-187">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="5eb44-188">`AcrValues`Właściwość jest sprawdzana pod kątem `mfa` wartości.</span><span class="sxs-lookup"><span data-stu-id="5eb44-188">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="5eb44-189">Jeśli `mfa` wartość jest obecna, uwierzytelnianie wieloskładnikowe jest wymuszane przed zakończeniem logowania (na przykład przekierowane do `ErrorEnable2FA` widoku).</span><span class="sxs-lookup"><span data-stu-id="5eb44-189">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="5eb44-190">Jeśli użytkownik jest już zalogowany, aplikacja kliencka:</span><span class="sxs-lookup"><span data-stu-id="5eb44-190">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="5eb44-191">Nadal sprawdza poprawność `amr` .</span><span class="sxs-lookup"><span data-stu-id="5eb44-191">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="5eb44-192">Usługę MFA można skonfigurować za pomocą linku do widoku ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5eb44-192">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="5eb44-194">Wymuś ASP.NET Core OpenID Connect Połącz klienta, aby wymagać uwierzytelniania wieloskładnikowego</span><span class="sxs-lookup"><span data-stu-id="5eb44-194">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="5eb44-195">Ten przykład pokazuje, w jaki sposób Razor aplikacja ASP.NET Core Page, która używa OpenID Connect Connect do logowania, może wymagać uwierzytelniania użytkowników przy użyciu usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-195">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="5eb44-196">Aby sprawdzić wymaganie usługi MFA, `IAuthorizationRequirement` jest tworzone wymaganie.</span><span class="sxs-lookup"><span data-stu-id="5eb44-196">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="5eb44-197">Ta wartość zostanie dodana do stron przy użyciu zasad, które wymagają uwierzytelniania wieloskładnikowego.</span><span class="sxs-lookup"><span data-stu-id="5eb44-197">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="5eb44-198">`AuthorizationHandler`Wdrożono, który będzie używać `amr` roszczeń i sprawdzać wartość `mfa` .</span><span class="sxs-lookup"><span data-stu-id="5eb44-198">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="5eb44-199">`amr`Jest zwracany w przypadku `id_token` pomyślnego uwierzytelnienia i może mieć wiele różnych wartości zgodnie z definicją w specyfikacji [wartości odwołania metody uwierzytelniania](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="5eb44-199">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="5eb44-200">Zwracana wartość zależy od tego, jak tożsamość została uwierzytelniona i w implementacji serwera OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="5eb44-200">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="5eb44-201">Program `AuthorizationHandler` używa `RequireMfa` wymagania i sprawdza poprawność tego `amr` żądania.</span><span class="sxs-lookup"><span data-stu-id="5eb44-201">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="5eb44-202">Serwer OpenID Connect Connect można zaimplementować przy użyciu Identity Server4 z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5eb44-202">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="5eb44-203">Gdy użytkownik loguje się przy użyciu TOTP, `amr` jest zwracana wartość usługi MFA.</span><span class="sxs-lookup"><span data-stu-id="5eb44-203">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="5eb44-204">Jeśli jest używana inna implementacja serwera OpenID Connect Connect lub inny typ usługi MFA, w ramach tego `amr` żądania lub może być używana inna wartość.</span><span class="sxs-lookup"><span data-stu-id="5eb44-204">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="5eb44-205">Kod musi być rozszerzony, aby można było go również zaakceptować.</span><span class="sxs-lookup"><span data-stu-id="5eb44-205">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="5eb44-206">W `Startup.ConfigureServices` metodzie `AddOpenIdConnect` Metoda jest używana jako domyślny schemat wyzwania.</span><span class="sxs-lookup"><span data-stu-id="5eb44-206">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="5eb44-207">Procedura obsługi autoryzacji, która jest używana do sprawdzania `amr` roszczeń, jest dodawana do kontenera kontroli Inversion.</span><span class="sxs-lookup"><span data-stu-id="5eb44-207">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="5eb44-208">Następnie tworzone są zasady, które dodaje `RequireMfa` wymaganie.</span><span class="sxs-lookup"><span data-stu-id="5eb44-208">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
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

    services.AddRazorPages();
}
```

<span data-ttu-id="5eb44-209">Te zasady są następnie używane na Razor stronie zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="5eb44-209">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="5eb44-210">Zasady mogą być również dodawane globalnie dla całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5eb44-210">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
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

<span data-ttu-id="5eb44-211">Jeśli użytkownik uwierzytelnia się bez uwierzytelniania wieloskładnikowego, `amr` będzie prawdopodobnie miał `pwd` wartość.</span><span class="sxs-lookup"><span data-stu-id="5eb44-211">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="5eb44-212">Żądanie nie zostanie autoryzowane do uzyskania dostępu do strony.</span><span class="sxs-lookup"><span data-stu-id="5eb44-212">The request won't be authorized to access the page.</span></span> <span data-ttu-id="5eb44-213">Przy użyciu wartości domyślnych użytkownik zostanie przekierowany na stronę *Account/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="5eb44-213">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="5eb44-214">Takie zachowanie można zmienić lub wdrożyć własną logikę niestandardową tutaj.</span><span class="sxs-lookup"><span data-stu-id="5eb44-214">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="5eb44-215">W tym przykładzie zostanie dodany link, dzięki czemu prawidłowy użytkownik może skonfigurować uwierzytelnianie wieloskładnikowe dla swojego konta.</span><span class="sxs-lookup"><span data-stu-id="5eb44-215">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="5eb44-216">Teraz tylko użytkownicy, którzy uwierzytelniają się za pomocą usługi MFA, mogą uzyskiwać dostęp do strony lub witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5eb44-216">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="5eb44-217">Jeśli używane są różne typy MFA lub jeśli funkcji 2FA jest poprawny, to w przypadku wystąpienia tego `amr` żądania będą mieć różne wartości i muszą zostać prawidłowo przetworzone.</span><span class="sxs-lookup"><span data-stu-id="5eb44-217">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="5eb44-218">Różne serwery OpenID Connect Connect również zwracają różne wartości dla tego żądania i mogą nie być zgodne ze specyfikacją [wartości odwołania metody uwierzytelniania](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="5eb44-218">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="5eb44-219">Logowanie bez uwierzytelniania wieloskładnikowego (na przykład przy użyciu hasła):</span><span class="sxs-lookup"><span data-stu-id="5eb44-219">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="5eb44-220">`amr`Ma `pwd` wartość:</span><span class="sxs-lookup"><span data-stu-id="5eb44-220">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="5eb44-222">Odmowa dostępu:</span><span class="sxs-lookup"><span data-stu-id="5eb44-222">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="5eb44-224">Alternatywnie, logowanie przy użyciu uwierzytelniania OTP z Identity :</span><span class="sxs-lookup"><span data-stu-id="5eb44-224">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="5eb44-226">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5eb44-226">Additional resources</span></span>

* [<span data-ttu-id="5eb44-227">Włącz generowanie kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb44-227">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="5eb44-228">Opcje uwierzytelniania bezhasło dla Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="5eb44-228">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="5eb44-229">FIDO2 .NET Library for FIDO2/WebAuthn zaświadczanie i potwierdzenie przy użyciu platformy .NET</span><span class="sxs-lookup"><span data-stu-id="5eb44-229">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="5eb44-230">WebAuthn awesome</span><span class="sxs-lookup"><span data-stu-id="5eb44-230">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
