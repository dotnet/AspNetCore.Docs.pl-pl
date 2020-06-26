---
title: Konfigurowanie ASP.NET CoreIdentity
author: AdrienTorris
description: Poznaj ASP.NET Core Identity wartości domyślne i Dowiedz się, jak skonfigurować Identity właściwości do używania wartości niestandardowych.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408971"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="75f3b-103">Konfigurowanie ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="75f3b-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="75f3b-104">ASP.NET Core Identity używa domyślnych wartości ustawień, takich jak zasady haseł, blokada i konfiguracja plików cookie.</span><span class="sxs-lookup"><span data-stu-id="75f3b-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="75f3b-105">Te ustawienia można przesłonić w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="75f3b-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a>Identity<span data-ttu-id="75f3b-106">Opcje</span><span class="sxs-lookup"><span data-stu-id="75f3b-106"> options</span></span>

<span data-ttu-id="75f3b-107">Klasa [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) reprezentuje opcje, których można użyć w celu skonfigurowania Identity systemu.</span><span class="sxs-lookup"><span data-stu-id="75f3b-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="75f3b-108">`IdentityOptions`musi być ustawiony **po** wywołaniu `AddIdentity` lub `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="75f3b-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="75f3b-109">OświadczeńIdentity</span><span class="sxs-lookup"><span data-stu-id="75f3b-109">Claims Identity</span></span>

<span data-ttu-id="75f3b-110">[IdentityOptions. Identyfikator oświadczenia](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) określa [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) z właściwościami podanymi w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="75f3b-111">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-111">Property</span></span> | <span data-ttu-id="75f3b-112">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-112">Description</span></span> | <span data-ttu-id="75f3b-113">Domyślne</span><span class="sxs-lookup"><span data-stu-id="75f3b-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="75f3b-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="75f3b-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="75f3b-115">Pobiera lub ustawia typ zgłoszenia używany dla żądania roli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="75f3b-116">Oświadczenia. rola</span><span class="sxs-lookup"><span data-stu-id="75f3b-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="75f3b-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="75f3b-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="75f3b-118">Pobiera lub ustawia typ zgłoszenia używany dla żądania sygnatury zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="75f3b-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="75f3b-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="75f3b-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="75f3b-120">Pobiera lub ustawia typ zgłoszenia używany dla żądania identyfikatora użytkownika.</span><span class="sxs-lookup"><span data-stu-id="75f3b-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="75f3b-121">Oświadczenia. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="75f3b-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="75f3b-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="75f3b-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="75f3b-123">Pobiera lub ustawia typ zgłoszenia używany jako nazwa użytkownika.</span><span class="sxs-lookup"><span data-stu-id="75f3b-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="75f3b-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="75f3b-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="75f3b-125">Blokad</span><span class="sxs-lookup"><span data-stu-id="75f3b-125">Lockout</span></span>

<span data-ttu-id="75f3b-126">Blokada została ustawiona w metodzie [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :</span><span class="sxs-lookup"><span data-stu-id="75f3b-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="75f3b-127">Poprzedni kod jest oparty na `Login` Identity szablonie.</span><span class="sxs-lookup"><span data-stu-id="75f3b-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="75f3b-128">Opcje blokady są ustawione w `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="75f3b-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="75f3b-129">Poprzedni kod ustawia [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) z wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="75f3b-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="75f3b-130">Pomyślne uwierzytelnienie resetuje liczbę prób dostępu zakończonych niepowodzeniem i resetuje zegar.</span><span class="sxs-lookup"><span data-stu-id="75f3b-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="75f3b-131">[IdentityOptions. Blokada](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) określa [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) z właściwościami przedstawionymi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="75f3b-132">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-132">Property</span></span> | <span data-ttu-id="75f3b-133">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-133">Description</span></span> | <span data-ttu-id="75f3b-134">Domyślne</span><span class="sxs-lookup"><span data-stu-id="75f3b-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="75f3b-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="75f3b-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="75f3b-136">Określa, czy nowy użytkownik może być zablokowany.</span><span class="sxs-lookup"><span data-stu-id="75f3b-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="75f3b-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="75f3b-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="75f3b-138">Ilość czasu, przez który użytkownik jest blokowany, gdy następuje blokada.</span><span class="sxs-lookup"><span data-stu-id="75f3b-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="75f3b-139">5 minut</span><span class="sxs-lookup"><span data-stu-id="75f3b-139">5 minutes</span></span> |
| [<span data-ttu-id="75f3b-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="75f3b-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="75f3b-141">Liczba nieudanych prób dostępu do momentu zablokowania użytkownika, jeśli włączono blokadę.</span><span class="sxs-lookup"><span data-stu-id="75f3b-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="75f3b-142">5</span><span class="sxs-lookup"><span data-stu-id="75f3b-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="75f3b-143">Hasło</span><span class="sxs-lookup"><span data-stu-id="75f3b-143">Password</span></span>

<span data-ttu-id="75f3b-144">Domyślnie program Identity wymaga, aby hasła zawierały wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne.</span><span class="sxs-lookup"><span data-stu-id="75f3b-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="75f3b-145">Długość hasła musi wynosić co najmniej 6 znaków.</span><span class="sxs-lookup"><span data-stu-id="75f3b-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="75f3b-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) można ustawić w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="75f3b-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="75f3b-147">[IdentityOptions. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) określa [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) z właściwościami podanymi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="75f3b-148">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-148">Property</span></span> | <span data-ttu-id="75f3b-149">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-149">Description</span></span> | <span data-ttu-id="75f3b-150">Domyślne</span><span class="sxs-lookup"><span data-stu-id="75f3b-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="75f3b-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="75f3b-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="75f3b-152">Wymaga liczby z zakresu od 0-9 do hasła.</span><span class="sxs-lookup"><span data-stu-id="75f3b-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="75f3b-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="75f3b-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="75f3b-154">Minimalna długość hasła.</span><span class="sxs-lookup"><span data-stu-id="75f3b-154">The minimum length of the password.</span></span> | <span data-ttu-id="75f3b-155">6</span><span class="sxs-lookup"><span data-stu-id="75f3b-155">6</span></span> |
| [<span data-ttu-id="75f3b-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="75f3b-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="75f3b-157">Wymaga małych liter w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="75f3b-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="75f3b-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="75f3b-159">Wymaga znaku niealfanumerycznego w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="75f3b-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="75f3b-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="75f3b-161">Dotyczy tylko ASP.NET Core 2,0 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="75f3b-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="75f3b-162">Wymaga podania liczby odrębnych znaków w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="75f3b-163">1</span><span class="sxs-lookup"><span data-stu-id="75f3b-163">1</span></span> |
| [<span data-ttu-id="75f3b-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="75f3b-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="75f3b-165">Wymaga wielkich liter w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="75f3b-166">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-166">Property</span></span> | <span data-ttu-id="75f3b-167">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-167">Description</span></span> | <span data-ttu-id="75f3b-168">Domyślne</span><span class="sxs-lookup"><span data-stu-id="75f3b-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="75f3b-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="75f3b-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="75f3b-170">Wymaga liczby z zakresu od 0-9 do hasła.</span><span class="sxs-lookup"><span data-stu-id="75f3b-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="75f3b-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="75f3b-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="75f3b-172">Minimalna długość hasła.</span><span class="sxs-lookup"><span data-stu-id="75f3b-172">The minimum length of the password.</span></span> | <span data-ttu-id="75f3b-173">6</span><span class="sxs-lookup"><span data-stu-id="75f3b-173">6</span></span> |
| [<span data-ttu-id="75f3b-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="75f3b-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="75f3b-175">Wymaga małych liter w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="75f3b-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="75f3b-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="75f3b-177">Wymaga znaku niealfanumerycznego w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="75f3b-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="75f3b-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="75f3b-179">Wymaga wielkich liter w haśle.</span><span class="sxs-lookup"><span data-stu-id="75f3b-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="75f3b-180">Logowanie</span><span class="sxs-lookup"><span data-stu-id="75f3b-180">Sign-in</span></span>

<span data-ttu-id="75f3b-181">Poniższy kod określa `SignIn` Ustawienia (do wartości domyślnych):</span><span class="sxs-lookup"><span data-stu-id="75f3b-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="75f3b-182">[IdentityOptions. Signer](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) określa [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) z właściwościami podanymi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="75f3b-183">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-183">Property</span></span> | <span data-ttu-id="75f3b-184">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-184">Description</span></span> | <span data-ttu-id="75f3b-185">Domyślne</span><span class="sxs-lookup"><span data-stu-id="75f3b-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="75f3b-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="75f3b-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="75f3b-187">Do zalogowania się wymaga potwierdzonej wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="75f3b-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="75f3b-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="75f3b-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="75f3b-189">Wymaga potwierdzenia numeru telefonu w celu zalogowania się.</span><span class="sxs-lookup"><span data-stu-id="75f3b-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="75f3b-190">Tokeny</span><span class="sxs-lookup"><span data-stu-id="75f3b-190">Tokens</span></span>

<span data-ttu-id="75f3b-191">[IdentityOptions. tokeny](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) określa [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) z właściwościami podanymi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="75f3b-192">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="75f3b-193">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="75f3b-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="75f3b-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="75f3b-195">Pobiera lub ustawia `AuthenticatorTokenProvider` używany do weryfikacji logowania dwuskładnikowego przy użyciu wystawcy uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="75f3b-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="75f3b-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="75f3b-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="75f3b-197">Pobiera lub ustawia `ChangeEmailTokenProvider` używany do generowania tokenów używanych w wiadomościach e-mail z potwierdzeniem zmiany wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="75f3b-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="75f3b-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="75f3b-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="75f3b-199">Pobiera lub ustawia `ChangePhoneNumberTokenProvider` używany do generowania tokenów używanych podczas zmieniania numerów telefonów.</span><span class="sxs-lookup"><span data-stu-id="75f3b-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="75f3b-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="75f3b-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="75f3b-201">Pobiera lub ustawia dostawcę tokenu używanego do generowania tokenów używanych w wiadomościach e-mail z potwierdzeniem konta.</span><span class="sxs-lookup"><span data-stu-id="75f3b-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="75f3b-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="75f3b-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="75f3b-203">Pobiera lub ustawia [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) używany do generowania tokenów używanych w wiadomościach e-mail dotyczących resetowania haseł.</span><span class="sxs-lookup"><span data-stu-id="75f3b-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="75f3b-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="75f3b-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="75f3b-205">Służy do konstruowania [dostawcy tokenów użytkownika](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) z kluczem używanym jako nazwa dostawcy.</span><span class="sxs-lookup"><span data-stu-id="75f3b-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="75f3b-206">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="75f3b-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="75f3b-207">[IdentityOptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) określa [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) z właściwościami podanymi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="75f3b-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="75f3b-208">Właściwość</span><span class="sxs-lookup"><span data-stu-id="75f3b-208">Property</span></span> | <span data-ttu-id="75f3b-209">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-209">Description</span></span> | <span data-ttu-id="75f3b-210">Domyślne</span><span class="sxs-lookup"><span data-stu-id="75f3b-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="75f3b-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="75f3b-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="75f3b-212">Dozwolone znaki w nazwie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="75f3b-212">Allowed characters in the username.</span></span> | <span data-ttu-id="75f3b-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="75f3b-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="75f3b-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="75f3b-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="75f3b-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="75f3b-215">0123456789</span></span><br><span data-ttu-id="75f3b-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="75f3b-216">-.\_@+</span></span> |
| [<span data-ttu-id="75f3b-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="75f3b-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="75f3b-218">Wymaga, aby każdy użytkownik miał unikatową wiadomość e-mail.</span><span class="sxs-lookup"><span data-stu-id="75f3b-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="75f3b-219">Ustawienia plików cookie</span><span class="sxs-lookup"><span data-stu-id="75f3b-219">Cookie settings</span></span>

<span data-ttu-id="75f3b-220">Skonfiguruj plik cookie aplikacji w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="75f3b-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="75f3b-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) musi być wywoływana **po** wywołaniu `AddIdentity` lub `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="75f3b-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="75f3b-222">Aby uzyskać więcej informacji, zobacz [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="75f3b-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="75f3b-223">Opcje skrótu hasła</span><span class="sxs-lookup"><span data-stu-id="75f3b-223">Password Hasher options</span></span>

<span data-ttu-id="75f3b-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Pobiera i ustawia opcje tworzenia skrótów haseł.</span><span class="sxs-lookup"><span data-stu-id="75f3b-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="75f3b-225">Opcja</span><span class="sxs-lookup"><span data-stu-id="75f3b-225">Option</span></span> | <span data-ttu-id="75f3b-226">Opis</span><span class="sxs-lookup"><span data-stu-id="75f3b-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="75f3b-227">Tryb zgodności używany podczas mieszania nowych haseł.</span><span class="sxs-lookup"><span data-stu-id="75f3b-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="75f3b-228">Wartość domyślna to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="75f3b-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="75f3b-229">Pierwszy bajt skrótu hasła zwanego *znacznikiem formatu*określa wersję algorytmu wyznaczania wartości skrótu używanego do mieszania hasła.</span><span class="sxs-lookup"><span data-stu-id="75f3b-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="75f3b-230">Podczas weryfikowania hasła przy użyciu skrótu <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> Metoda wybiera poprawny algorytm na podstawie pierwszego bajtu.</span><span class="sxs-lookup"><span data-stu-id="75f3b-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="75f3b-231">Klient może się uwierzytelnić niezależnie od tego, która wersja algorytmu została użyta do skrótu hasła.</span><span class="sxs-lookup"><span data-stu-id="75f3b-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="75f3b-232">Ustawienie trybu zgodności ma wpływ na skróty *nowych haseł*.</span><span class="sxs-lookup"><span data-stu-id="75f3b-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="75f3b-233">Liczba iteracji używanych podczas tworzenia skrótów haseł przy użyciu PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="75f3b-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="75f3b-234">Ta wartość jest używana tylko wtedy, gdy <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> jest ustawiona na <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="75f3b-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="75f3b-235">Wartość musi być dodatnią liczbą całkowitą i wartością domyślną `10000` .</span><span class="sxs-lookup"><span data-stu-id="75f3b-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="75f3b-236">W poniższym przykładzie <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> jest ustawiona na wartość `12000` w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="75f3b-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
