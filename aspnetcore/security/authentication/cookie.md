---
title: Użyj cookie uwierzytelniania bez ASP.NET Core Identity
author: rick-anderson
description: Dowiedz się, jak używać cookie uwierzytelniania bez ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
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
uid: security/authentication/cookie
ms.openlocfilehash: 2e9eb58837d74343d8de6903372146570b43f330
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627146"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="0efd3-103">Użyj cookie uwierzytelniania bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="0efd3-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="0efd3-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0efd3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0efd3-105">ASP.NET Core Identity jest kompletnym, w pełni funkcjonalnym dostawcą uwierzytelniania na potrzeby tworzenia i obsługi logowania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="0efd3-106">cookieNie można jednak użyć dostawcy uwierzytelniania opartego na usłudze ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="0efd3-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="0efd3-107">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="0efd3-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="0efd3-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0efd3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0efd3-109">W celach demonstracyjnych w przykładowej aplikacji konto użytkownika dla hipotetycznego użytkownika, Maria Rodriguez jest stałe do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="0efd3-110">Zaloguj się przy użyciu adresu **e-mail** `maria.rodriguez@contoso.com` i hasła.</span><span class="sxs-lookup"><span data-stu-id="0efd3-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="0efd3-111">Użytkownik jest uwierzytelniany w `AuthenticateUser` metodzie w pliku *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="0efd3-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="0efd3-112">W świecie rzeczywistym użytkownik zostanie uwierzytelniony w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0efd3-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="0efd3-113">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="0efd3-113">Configuration</span></span>

<span data-ttu-id="0efd3-114">Jeśli aplikacja nie korzysta z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu w pliku projektu dla elementu [Microsoft. AspNetCore. Authentication. Cookie Pakiet s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="0efd3-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="0efd3-115">W `Startup.ConfigureServices` metodzie Utwórz usługi pośredniczące uwierzytelniania przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod i:</span><span class="sxs-lookup"><span data-stu-id="0efd3-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="0efd3-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> przeszedł do `AddAuthentication` Ustawia domyślny schemat uwierzytelniania dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="0efd3-117">`AuthenticationScheme` jest przydatne, gdy istnieje wiele wystąpień cookie uwierzytelniania i chcesz [autoryzować z określonym schematem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="0efd3-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="0efd3-118">Ustawienie parametru `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) zapewnia wartość " Cookie s" dla schematu.</span><span class="sxs-lookup"><span data-stu-id="0efd3-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="0efd3-119">Można podać dowolną wartość ciągu odróżniającą schemat.</span><span class="sxs-lookup"><span data-stu-id="0efd3-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="0efd3-120">Schemat uwierzytelniania aplikacji różni się od cookie schematu uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="0efd3-121">Gdy cookie schemat uwierzytelniania nie jest dostarczany <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , używa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="0efd3-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="0efd3-122">Właściwość uwierzytelnianie cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> jest domyślnie ustawiona na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="0efd3-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="0efd3-123">Uwierzytelnianie cookie s jest dozwolone, gdy odwiedzający witrynę nie wyraził zgodę na zbieranie danych.</span><span class="sxs-lookup"><span data-stu-id="0efd3-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="0efd3-124">Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="0efd3-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="0efd3-125">W `Startup.Configure` , wywołaj `UseAuthentication` i, `UseAuthorization` Aby ustawić `HttpContext.User` Właściwość i uruchamiać oprogramowanie pośredniczące autoryzacji dla żądań.</span><span class="sxs-lookup"><span data-stu-id="0efd3-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="0efd3-126">Wywołaj `UseAuthentication` `UseAuthorization` metody i przed wywołaniem `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="0efd3-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="0efd3-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Klasa służy do konfigurowania opcji dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="0efd3-128">Ustaw `CookieAuthenticationOptions` w konfiguracji usługi na potrzeby uwierzytelniania w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="0efd3-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="0efd3-129">Cookie Oprogramowanie pośredniczące zasad</span><span class="sxs-lookup"><span data-stu-id="0efd3-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="0efd3-130">[ Cookie Oprogramowanie pośredniczące zasad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umożliwia korzystanie z cookie możliwości zasad.</span><span class="sxs-lookup"><span data-stu-id="0efd3-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="0efd3-131">Dodanie oprogramowania pośredniczącego do potoku przetwarzania aplikacji jest zgodne z kolejnością, ma &mdash; wpływ tylko na składniki podrzędne zarejestrowane w potoku.</span><span class="sxs-lookup"><span data-stu-id="0efd3-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="0efd3-132">Użycie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> zapewniane przez Cookie oprogramowanie pośredniczące zasad do sterowania globalnymi charakterystykami cookie przetwarzania i cookie podłączania do programów obsługi przetwarzania, gdy cookie s są dodawane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="0efd3-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="0efd3-133">Wartością domyślną <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> jest `SameSiteMode.Lax` umożliwienie uwierzytelniania OAuth2.</span><span class="sxs-lookup"><span data-stu-id="0efd3-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="0efd3-134">Aby ściśle wymuszać zasady dotyczące tej samej lokacji `SameSiteMode.Strict` , należy ustawić `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="0efd3-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="0efd3-135">Mimo że to ustawienie powoduje przerwanie OAuth2 i innych schematów uwierzytelniania między źródłami, podnosi poziom cookie zabezpieczeń dla innych typów aplikacji, które nie polegają na przetwarzaniu żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="0efd3-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="0efd3-136">CookieUstawienie ustawienia pośredniczące zasad dla programu `MinimumSameSitePolicy` może mieć wpływ na ustawienia `Cookie.SameSite` w `CookieAuthenticationOptions` ustawieniach zgodnie z poniższą macierzą.</span><span class="sxs-lookup"><span data-stu-id="0efd3-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="0efd3-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="0efd3-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="0efd3-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="0efd3-138">Cookie.SameSite</span></span> | <span data-ttu-id="0efd3-139">Wynik Cookie . Ustawienie SameSite</span><span class="sxs-lookup"><span data-stu-id="0efd3-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="0efd3-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-140">SameSiteMode.None</span></span>     | <span data-ttu-id="0efd3-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-141">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-142">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="0efd3-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-144">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-145">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="0efd3-147">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="0efd3-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-148">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-149">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="0efd3-151">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-152">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="0efd3-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="0efd3-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-155">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-156">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="0efd3-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="0efd3-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="0efd3-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="0efd3-161">Tworzenie uwierzytelniania cookie</span><span class="sxs-lookup"><span data-stu-id="0efd3-161">Create an authentication cookie</span></span>

<span data-ttu-id="0efd3-162">Aby utworzyć cookie Informacje o użytkowniku, konstrukcja a <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="0efd3-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="0efd3-163">Informacje o użytkowniku są serializowane i przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="0efd3-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="0efd3-164">Utwórz <xref:System.Security.Claims.ClaimsIdentity> z dowolnym wymaganym elementem <xref:System.Security.Claims.Claim> s i Wywołaj, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Aby zalogować użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0efd3-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="0efd3-165">`SignInAsync` tworzy zaszyfrowaną cookie i dodaje ją do bieżącej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0efd3-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="0efd3-166">Jeśli `AuthenticationScheme` nie jest określony, używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="0efd3-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="0efd3-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> jest używany tylko w przypadku kilku konkretnych ścieżek domyślnie, na przykład ścieżki logowania i ścieżki wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="0efd3-168">Aby uzyskać więcej informacji, zobacz [ Cookie Źródło AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="0efd3-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="0efd3-169">System [ochrony danych](xref:security/data-protection/using-data-protection) ASP.NET Core jest używany do szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="0efd3-170">W przypadku aplikacji hostowanej na wielu komputerach, równoważenia obciążenia między aplikacjami lub korzystania z kolektywu serwerów sieci Web należy [skonfigurować ochronę danych](xref:security/data-protection/configuration/overview) tak, aby używała tego samego dzwonka klucza i identyfikatora aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="0efd3-171">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="0efd3-171">Sign out</span></span>

<span data-ttu-id="0efd3-172">Aby wylogować bieżącego użytkownika i usunąć cookie połączenie, wywołaj <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="0efd3-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="0efd3-173">Jeśli `CookieAuthenticationDefaults.AuthenticationScheme` (lub " Cookie s") nie jest używany jako schemat (na przykład "contoso Cookie "), podaj Schemat używany podczas konfigurowania dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="0efd3-174">W przeciwnym razie używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="0efd3-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="0efd3-175">Reagowanie na zmiany zaplecza</span><span class="sxs-lookup"><span data-stu-id="0efd3-175">React to back-end changes</span></span>

<span data-ttu-id="0efd3-176">Gdy cookie zostanie utworzony, cookie jest to pojedyncze Źródło tożsamości.</span><span class="sxs-lookup"><span data-stu-id="0efd3-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="0efd3-177">Jeśli konto użytkownika jest wyłączone w systemach zaplecza:</span><span class="sxs-lookup"><span data-stu-id="0efd3-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="0efd3-178">cookieSystem uwierzytelniania aplikacji kontynuuje przetwarzanie żądań na podstawie uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="0efd3-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="0efd3-179">Użytkownik pozostaje zalogowany do aplikacji, o ile uwierzytelnianie cookie jest prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="0efd3-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="0efd3-180"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Zdarzenie może służyć do przechwycenia i zastąpienia weryfikacji cookie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="0efd3-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="0efd3-181">Sprawdzanie poprawności cookie na każde żądanie zmniejsza ryzyko odwołujący się użytkowników, którzy uzyskują dostęp do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="0efd3-182">Jedno z metod cookie weryfikacji polega na śledzeniu zmian w bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0efd3-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="0efd3-183">Jeśli baza danych nie została zmieniona od czasu cookie wystawienia użytkownika, nie ma potrzeby ponownego uwierzytelnienia użytkownika, jeśli cookie jest on nadal ważny.</span><span class="sxs-lookup"><span data-stu-id="0efd3-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="0efd3-184">W przykładowej aplikacji baza danych jest zaimplementowana w `IUserRepository` i przechowuje `LastChanged` wartość.</span><span class="sxs-lookup"><span data-stu-id="0efd3-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="0efd3-185">Gdy użytkownik zostanie zaktualizowany w bazie danych, `LastChanged` wartość jest ustawiana na bieżącą godzinę.</span><span class="sxs-lookup"><span data-stu-id="0efd3-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="0efd3-186">Aby unieważnić cookie , kiedy baza danych zmienia się w zależności od `LastChanged` wartości, Utwórz cookie za pomocą `LastChanged` żądania zawierającego bieżącą `LastChanged` wartość z bazy danych:</span><span class="sxs-lookup"><span data-stu-id="0efd3-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="0efd3-187">Aby zaimplementować przesłonięcie dla `ValidatePrincipal` zdarzenia, napisz metodę o następującym podpisie w klasie, która pochodzi od <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="0efd3-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="0efd3-188">Poniżej przedstawiono przykładową implementację programu `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="0efd3-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="0efd3-189">Zarejestruj wystąpienie zdarzeń podczas cookie rejestracji usługi w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="0efd3-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0efd3-190">Podaj [rejestrację usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes) dla swojej `CustomCookieAuthenticationEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="0efd3-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="0efd3-191">Rozważ sytuację, w której nazwa użytkownika jest aktualizowana &mdash; decyzją, która nie ma wpływu na zabezpieczenia w jakikolwiek sposób.</span><span class="sxs-lookup"><span data-stu-id="0efd3-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="0efd3-192">Jeśli chcesz nieszkodliwej aktualizacji podmiotu zabezpieczeń, wywołaj `context.ReplacePrincipal` i ustaw `context.ShouldRenew` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="0efd3-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="0efd3-193">Opisane tutaj podejście jest wyzwalane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="0efd3-194">Sprawdzanie poprawności uwierzytelniania cookie s dla wszystkich użytkowników w każdym żądaniu może spowodować spadek wydajności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="0efd3-195">Trwałe cookie s</span><span class="sxs-lookup"><span data-stu-id="0efd3-195">Persistent cookies</span></span>

<span data-ttu-id="0efd3-196">Może zaistnieć potrzeba cookie utrwalenia sesji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0efd3-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="0efd3-197">Tę trwałość należy włączyć tylko w przypadku jawnej zgody użytkownika przy użyciu pola wyboru "Zapamiętaj mnie" przy logowaniu lub podobnym mechanizmie.</span><span class="sxs-lookup"><span data-stu-id="0efd3-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="0efd3-198">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie za przechodzenie przez zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0efd3-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="0efd3-199">Wszystkie skonfigurowane wcześniej ustawienia wygasania są honorowane.</span><span class="sxs-lookup"><span data-stu-id="0efd3-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="0efd3-200">Jeśli cookie wygaśnie, gdy przeglądarka zostanie ZAMKNIĘTA, przeglądarka czyści cookie po jej ponownym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="0efd3-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="0efd3-201">Ustaw <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na wartość `true` w <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="0efd3-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="0efd3-202">Bezwzględne cookie wygaśnięcie</span><span class="sxs-lookup"><span data-stu-id="0efd3-202">Absolute cookie expiration</span></span>

<span data-ttu-id="0efd3-203">Bezwzględny czas wygaśnięcia można ustawić za pomocą <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="0efd3-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="0efd3-204">Aby utworzyć trwały cookie , `IsPersistent` należy również ustawić wartość.</span><span class="sxs-lookup"><span data-stu-id="0efd3-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="0efd3-205">W przeciwnym razie cookie jest tworzona przy użyciu okresu istnienia sesji i może wygasnąć przed lub po posiadanym przez niego biletem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="0efd3-206">Gdy `ExpiresUtc` jest ustawiona, zastępuje wartość <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opcji <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , jeśli jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="0efd3-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="0efd3-207">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="0efd3-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="0efd3-208">Ignoruje to wszystkie ustawienia wygasania, które zostały wcześniej skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="0efd3-208">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0efd3-209">ASP.NET Core Identity jest kompletnym, w pełni funkcjonalnym dostawcą uwierzytelniania na potrzeby tworzenia i obsługi logowania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="0efd3-210">Jednak cookie dostawcy uwierzytelniania uwierzytelniania opartego na uwierzytelnianiu nie ASP.NET Core Identity można używać.</span><span class="sxs-lookup"><span data-stu-id="0efd3-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="0efd3-211">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="0efd3-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="0efd3-212">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0efd3-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0efd3-213">W celach demonstracyjnych w przykładowej aplikacji konto użytkownika dla hipotetycznego użytkownika, Maria Rodriguez jest stałe do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="0efd3-214">Zaloguj się przy użyciu adresu **e-mail** `maria.rodriguez@contoso.com` i hasła.</span><span class="sxs-lookup"><span data-stu-id="0efd3-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="0efd3-215">Użytkownik jest uwierzytelniany w `AuthenticateUser` metodzie w pliku *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="0efd3-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="0efd3-216">W świecie rzeczywistym użytkownik zostanie uwierzytelniony w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0efd3-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="0efd3-217">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="0efd3-217">Configuration</span></span>

<span data-ttu-id="0efd3-218">Jeśli aplikacja nie korzysta z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu w pliku projektu dla elementu [Microsoft. AspNetCore. Authentication. Cookie Pakiet s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="0efd3-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="0efd3-219">W `Startup.ConfigureServices` metodzie Utwórz usługę pośredniczącą uwierzytelniania przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod i:</span><span class="sxs-lookup"><span data-stu-id="0efd3-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="0efd3-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> przeszedł do `AddAuthentication` Ustawia domyślny schemat uwierzytelniania dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="0efd3-221">`AuthenticationScheme` jest przydatne, gdy istnieje wiele wystąpień cookie uwierzytelniania i chcesz [autoryzować z określonym schematem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="0efd3-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="0efd3-222">Ustawienie parametru `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) zapewnia wartość " Cookie s" dla schematu.</span><span class="sxs-lookup"><span data-stu-id="0efd3-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="0efd3-223">Można podać dowolną wartość ciągu odróżniającą schemat.</span><span class="sxs-lookup"><span data-stu-id="0efd3-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="0efd3-224">Schemat uwierzytelniania aplikacji różni się od cookie schematu uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="0efd3-225">Gdy cookie schemat uwierzytelniania nie jest dostarczany <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , używa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="0efd3-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="0efd3-226">Właściwość uwierzytelnianie cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> jest domyślnie ustawiona na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="0efd3-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="0efd3-227">Uwierzytelnianie cookie s jest dozwolone, gdy odwiedzający witrynę nie wyraził zgodę na zbieranie danych.</span><span class="sxs-lookup"><span data-stu-id="0efd3-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="0efd3-228">Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="0efd3-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="0efd3-229">W `Startup.Configure` metodzie Wywołaj `UseAuthentication` metodę, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia `HttpContext.User` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="0efd3-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="0efd3-230">Wywołaj `UseAuthentication` metodę przed wywołaniem `UseMvcWithDefaultRoute` lub `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="0efd3-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="0efd3-231"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Klasa służy do konfigurowania opcji dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="0efd3-232">Ustaw `CookieAuthenticationOptions` w konfiguracji usługi na potrzeby uwierzytelniania w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="0efd3-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="0efd3-233">Cookie Oprogramowanie pośredniczące zasad</span><span class="sxs-lookup"><span data-stu-id="0efd3-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="0efd3-234">[ Cookie Oprogramowanie pośredniczące zasad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umożliwia korzystanie z cookie możliwości zasad.</span><span class="sxs-lookup"><span data-stu-id="0efd3-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="0efd3-235">Dodanie oprogramowania pośredniczącego do potoku przetwarzania aplikacji jest zgodne z kolejnością, ma &mdash; wpływ tylko na składniki podrzędne zarejestrowane w potoku.</span><span class="sxs-lookup"><span data-stu-id="0efd3-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="0efd3-236">Użycie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> zapewniane przez Cookie oprogramowanie pośredniczące zasad do sterowania globalnymi charakterystykami cookie przetwarzania i cookie podłączania do programów obsługi przetwarzania, gdy cookie s są dodawane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="0efd3-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="0efd3-237">Wartością domyślną <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> jest `SameSiteMode.Lax` umożliwienie uwierzytelniania OAuth2.</span><span class="sxs-lookup"><span data-stu-id="0efd3-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="0efd3-238">Aby ściśle wymuszać zasady dotyczące tej samej lokacji `SameSiteMode.Strict` , należy ustawić `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="0efd3-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="0efd3-239">Mimo że to ustawienie powoduje przerwanie OAuth2 i innych schematów uwierzytelniania między źródłami, podnosi poziom cookie zabezpieczeń dla innych typów aplikacji, które nie polegają na przetwarzaniu żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="0efd3-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="0efd3-240">CookieUstawienie ustawienia pośredniczące zasad dla programu `MinimumSameSitePolicy` może mieć wpływ na ustawienia `Cookie.SameSite` w `CookieAuthenticationOptions` ustawieniach zgodnie z poniższą macierzą.</span><span class="sxs-lookup"><span data-stu-id="0efd3-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="0efd3-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="0efd3-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="0efd3-242">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="0efd3-242">Cookie.SameSite</span></span> | <span data-ttu-id="0efd3-243">Wynik Cookie . Ustawienie SameSite</span><span class="sxs-lookup"><span data-stu-id="0efd3-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="0efd3-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-244">SameSiteMode.None</span></span>     | <span data-ttu-id="0efd3-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-245">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-246">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="0efd3-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-248">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-249">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="0efd3-251">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="0efd3-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-252">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-253">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="0efd3-255">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-256">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="0efd3-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="0efd3-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="0efd3-259">SameSiteMode.None</span></span><br><span data-ttu-id="0efd3-260">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="0efd3-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="0efd3-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="0efd3-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="0efd3-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="0efd3-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="0efd3-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="0efd3-265">Tworzenie uwierzytelniania cookie</span><span class="sxs-lookup"><span data-stu-id="0efd3-265">Create an authentication cookie</span></span>

<span data-ttu-id="0efd3-266">Aby utworzyć cookie Informacje o użytkowniku, konstrukcja a <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="0efd3-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="0efd3-267">Informacje o użytkowniku są serializowane i przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="0efd3-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="0efd3-268">Utwórz <xref:System.Security.Claims.ClaimsIdentity> z dowolnym wymaganym elementem <xref:System.Security.Claims.Claim> s i Wywołaj, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Aby zalogować użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0efd3-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="0efd3-269">`SignInAsync` tworzy zaszyfrowaną cookie i dodaje ją do bieżącej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0efd3-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="0efd3-270">Jeśli `AuthenticationScheme` nie jest określony, używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="0efd3-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="0efd3-271">System [ochrony danych](xref:security/data-protection/using-data-protection) ASP.NET Core jest używany do szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="0efd3-272">W przypadku aplikacji hostowanej na wielu komputerach, równoważenia obciążenia między aplikacjami lub korzystania z kolektywu serwerów sieci Web należy [skonfigurować ochronę danych](xref:security/data-protection/configuration/overview) tak, aby używała tego samego dzwonka klucza i identyfikatora aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="0efd3-273">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="0efd3-273">Sign out</span></span>

<span data-ttu-id="0efd3-274">Aby wylogować bieżącego użytkownika i usunąć cookie połączenie, wywołaj <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="0efd3-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="0efd3-275">Jeśli `CookieAuthenticationDefaults.AuthenticationScheme` (lub " Cookie s") nie jest używany jako schemat (na przykład "contoso Cookie "), podaj Schemat używany podczas konfigurowania dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="0efd3-276">W przeciwnym razie używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="0efd3-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="0efd3-277">Reagowanie na zmiany zaplecza</span><span class="sxs-lookup"><span data-stu-id="0efd3-277">React to back-end changes</span></span>

<span data-ttu-id="0efd3-278">Gdy cookie zostanie utworzony, cookie jest to pojedyncze Źródło tożsamości.</span><span class="sxs-lookup"><span data-stu-id="0efd3-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="0efd3-279">Jeśli konto użytkownika jest wyłączone w systemach zaplecza:</span><span class="sxs-lookup"><span data-stu-id="0efd3-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="0efd3-280">cookieSystem uwierzytelniania aplikacji kontynuuje przetwarzanie żądań na podstawie uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="0efd3-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="0efd3-281">Użytkownik pozostaje zalogowany do aplikacji, o ile uwierzytelnianie cookie jest prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="0efd3-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="0efd3-282"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Zdarzenie może służyć do przechwycenia i zastąpienia weryfikacji cookie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="0efd3-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="0efd3-283">Sprawdzanie poprawności cookie na każde żądanie zmniejsza ryzyko odwołujący się użytkowników, którzy uzyskują dostęp do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="0efd3-284">Jedno z metod cookie weryfikacji polega na śledzeniu zmian w bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0efd3-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="0efd3-285">Jeśli baza danych nie została zmieniona od czasu cookie wystawienia użytkownika, nie ma potrzeby ponownego uwierzytelnienia użytkownika, jeśli cookie jest on nadal ważny.</span><span class="sxs-lookup"><span data-stu-id="0efd3-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="0efd3-286">W przykładowej aplikacji baza danych jest zaimplementowana w `IUserRepository` i przechowuje `LastChanged` wartość.</span><span class="sxs-lookup"><span data-stu-id="0efd3-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="0efd3-287">Gdy użytkownik zostanie zaktualizowany w bazie danych, `LastChanged` wartość jest ustawiana na bieżącą godzinę.</span><span class="sxs-lookup"><span data-stu-id="0efd3-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="0efd3-288">Aby unieważnić cookie , kiedy baza danych zmienia się w zależności od `LastChanged` wartości, Utwórz cookie za pomocą `LastChanged` żądania zawierającego bieżącą `LastChanged` wartość z bazy danych:</span><span class="sxs-lookup"><span data-stu-id="0efd3-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="0efd3-289">Aby zaimplementować przesłonięcie dla `ValidatePrincipal` zdarzenia, napisz metodę o następującym podpisie w klasie, która pochodzi od <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="0efd3-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="0efd3-290">Poniżej przedstawiono przykładową implementację programu `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="0efd3-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="0efd3-291">Zarejestruj wystąpienie zdarzeń podczas cookie rejestracji usługi w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="0efd3-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0efd3-292">Podaj [rejestrację usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes) dla swojej `CustomCookieAuthenticationEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="0efd3-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="0efd3-293">Rozważ sytuację, w której nazwa użytkownika jest aktualizowana &mdash; decyzją, która nie ma wpływu na zabezpieczenia w jakikolwiek sposób.</span><span class="sxs-lookup"><span data-stu-id="0efd3-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="0efd3-294">Jeśli chcesz nieszkodliwej aktualizacji podmiotu zabezpieczeń, wywołaj `context.ReplacePrincipal` i ustaw `context.ShouldRenew` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="0efd3-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="0efd3-295">Opisane tutaj podejście jest wyzwalane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="0efd3-296">Sprawdzanie poprawności uwierzytelniania cookie s dla wszystkich użytkowników w każdym żądaniu może spowodować spadek wydajności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0efd3-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="0efd3-297">Trwałe cookie s</span><span class="sxs-lookup"><span data-stu-id="0efd3-297">Persistent cookies</span></span>

<span data-ttu-id="0efd3-298">Może zaistnieć potrzeba cookie utrwalenia sesji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0efd3-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="0efd3-299">Tę trwałość należy włączyć tylko w przypadku jawnej zgody użytkownika przy użyciu pola wyboru "Zapamiętaj mnie" przy logowaniu lub podobnym mechanizmie.</span><span class="sxs-lookup"><span data-stu-id="0efd3-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="0efd3-300">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie za przechodzenie przez zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0efd3-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="0efd3-301">Wszystkie skonfigurowane wcześniej ustawienia wygasania są honorowane.</span><span class="sxs-lookup"><span data-stu-id="0efd3-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="0efd3-302">Jeśli cookie wygaśnie, gdy przeglądarka zostanie ZAMKNIĘTA, przeglądarka czyści cookie po jej ponownym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="0efd3-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="0efd3-303">Ustaw <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na wartość `true` w <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="0efd3-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="0efd3-304">Bezwzględne cookie wygaśnięcie</span><span class="sxs-lookup"><span data-stu-id="0efd3-304">Absolute cookie expiration</span></span>

<span data-ttu-id="0efd3-305">Bezwzględny czas wygaśnięcia można ustawić za pomocą <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="0efd3-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="0efd3-306">Aby utworzyć trwały cookie , `IsPersistent` należy również ustawić wartość.</span><span class="sxs-lookup"><span data-stu-id="0efd3-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="0efd3-307">W przeciwnym razie cookie jest tworzona przy użyciu okresu istnienia sesji i może wygasnąć przed lub po posiadanym przez niego biletem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0efd3-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="0efd3-308">Gdy `ExpiresUtc` jest ustawiona, zastępuje wartość <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opcji <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , jeśli jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="0efd3-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="0efd3-309">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="0efd3-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="0efd3-310">Ignoruje to wszystkie ustawienia wygasania, które zostały wcześniej skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="0efd3-310">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0efd3-311">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0efd3-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="0efd3-312">Kontrole ról oparte na zasadach</span><span class="sxs-lookup"><span data-stu-id="0efd3-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
