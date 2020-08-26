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
ms.openlocfilehash: 04d2f0d289e2c9ec13aeb880df47240bec19d3ec
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876766"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="49dc2-103">Użyj cookie uwierzytelniania bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="49dc2-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="49dc2-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="49dc2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49dc2-105">ASP.NET Core Identity jest kompletnym, w pełni funkcjonalnym dostawcą uwierzytelniania na potrzeby tworzenia i obsługi logowania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="49dc2-106">cookieNie można jednak użyć dostawcy uwierzytelniania opartego na usłudze ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="49dc2-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="49dc2-107">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="49dc2-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="49dc2-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49dc2-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="49dc2-109">W celach demonstracyjnych w przykładowej aplikacji konto użytkownika dla hipotetycznego użytkownika, Maria Rodriguez jest stałe do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="49dc2-110">Zaloguj się przy użyciu adresu **e-mail** `maria.rodriguez@contoso.com` i hasła.</span><span class="sxs-lookup"><span data-stu-id="49dc2-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="49dc2-111">Użytkownik jest uwierzytelniany w `AuthenticateUser` metodzie w pliku *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="49dc2-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="49dc2-112">W świecie rzeczywistym użytkownik zostanie uwierzytelniony w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="49dc2-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="49dc2-113">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="49dc2-113">Configuration</span></span>

<span data-ttu-id="49dc2-114">Jeśli aplikacja nie korzysta z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu w pliku projektu dla elementu [Microsoft. AspNetCore. Authentication. Cookie Pakiet s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="49dc2-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="49dc2-115">W `Startup.ConfigureServices` metodzie Utwórz usługi pośredniczące uwierzytelniania przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod i:</span><span class="sxs-lookup"><span data-stu-id="49dc2-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="49dc2-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> przeszedł do `AddAuthentication` Ustawia domyślny schemat uwierzytelniania dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="49dc2-117">`AuthenticationScheme` jest przydatne, gdy istnieje wiele wystąpień cookie uwierzytelniania i chcesz [autoryzować z określonym schematem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="49dc2-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="49dc2-118">Ustawienie parametru `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) zapewnia wartość " Cookie s" dla schematu.</span><span class="sxs-lookup"><span data-stu-id="49dc2-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="49dc2-119">Można podać dowolną wartość ciągu odróżniającą schemat.</span><span class="sxs-lookup"><span data-stu-id="49dc2-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="49dc2-120">Schemat uwierzytelniania aplikacji różni się od cookie schematu uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="49dc2-121">Gdy cookie schemat uwierzytelniania nie jest dostarczany <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , używa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="49dc2-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="49dc2-122">Właściwość uwierzytelnianie cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> jest domyślnie ustawiona na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="49dc2-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="49dc2-123">Uwierzytelnianie cookie s jest dozwolone, gdy odwiedzający witrynę nie wyraził zgodę na zbieranie danych.</span><span class="sxs-lookup"><span data-stu-id="49dc2-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="49dc2-124">Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="49dc2-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="49dc2-125">W `Startup.Configure` , wywołaj `UseAuthentication` i, `UseAuthorization` Aby ustawić `HttpContext.User` Właściwość i uruchamiać oprogramowanie pośredniczące autoryzacji dla żądań.</span><span class="sxs-lookup"><span data-stu-id="49dc2-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="49dc2-126">Wywołaj `UseAuthentication` `UseAuthorization` metody i przed wywołaniem `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="49dc2-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="49dc2-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Klasa służy do konfigurowania opcji dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="49dc2-128">Ustaw `CookieAuthenticationOptions` w konfiguracji usługi na potrzeby uwierzytelniania w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="49dc2-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="49dc2-129">Cookie Oprogramowanie pośredniczące zasad</span><span class="sxs-lookup"><span data-stu-id="49dc2-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="49dc2-130">[ Cookie Oprogramowanie pośredniczące zasad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umożliwia korzystanie z cookie możliwości zasad.</span><span class="sxs-lookup"><span data-stu-id="49dc2-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="49dc2-131">Dodanie oprogramowania pośredniczącego do potoku przetwarzania aplikacji jest zgodne z kolejnością, ma &mdash; wpływ tylko na składniki podrzędne zarejestrowane w potoku.</span><span class="sxs-lookup"><span data-stu-id="49dc2-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="49dc2-132">Użycie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> zapewniane przez Cookie oprogramowanie pośredniczące zasad do sterowania globalnymi charakterystykami cookie przetwarzania i cookie podłączania do programów obsługi przetwarzania, gdy cookie s są dodawane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="49dc2-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="49dc2-133">Wartością domyślną <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> jest `SameSiteMode.Lax` umożliwienie uwierzytelniania OAuth2.</span><span class="sxs-lookup"><span data-stu-id="49dc2-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="49dc2-134">Aby ściśle wymuszać zasady dotyczące tej samej lokacji `SameSiteMode.Strict` , należy ustawić `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="49dc2-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="49dc2-135">Mimo że to ustawienie powoduje przerwanie OAuth2 i innych schematów uwierzytelniania między źródłami, podnosi poziom cookie zabezpieczeń dla innych typów aplikacji, które nie polegają na przetwarzaniu żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="49dc2-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="49dc2-136">CookieUstawienie ustawienia pośredniczące zasad dla programu `MinimumSameSitePolicy` może mieć wpływ na ustawienia `Cookie.SameSite` w `CookieAuthenticationOptions` ustawieniach zgodnie z poniższą macierzą.</span><span class="sxs-lookup"><span data-stu-id="49dc2-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="49dc2-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="49dc2-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="49dc2-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="49dc2-138">Cookie.SameSite</span></span> | <span data-ttu-id="49dc2-139">Wynik Cookie . Ustawienie SameSite</span><span class="sxs-lookup"><span data-stu-id="49dc2-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="49dc2-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-140">SameSiteMode.None</span></span>     | <span data-ttu-id="49dc2-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-141">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-142">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="49dc2-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-144">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-145">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="49dc2-147">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="49dc2-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-148">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-149">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="49dc2-151">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-152">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="49dc2-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="49dc2-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-155">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-156">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="49dc2-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="49dc2-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="49dc2-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="49dc2-161">Tworzenie uwierzytelniania cookie</span><span class="sxs-lookup"><span data-stu-id="49dc2-161">Create an authentication cookie</span></span>

<span data-ttu-id="49dc2-162">Aby utworzyć cookie Informacje o użytkowniku, konstrukcja a <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="49dc2-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="49dc2-163">Informacje o użytkowniku są serializowane i przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="49dc2-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="49dc2-164">Utwórz <xref:System.Security.Claims.ClaimsIdentity> z dowolnym wymaganym elementem <xref:System.Security.Claims.Claim> s i Wywołaj, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Aby zalogować użytkownika:</span><span class="sxs-lookup"><span data-stu-id="49dc2-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="49dc2-165">`SignInAsync` tworzy zaszyfrowaną cookie i dodaje ją do bieżącej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="49dc2-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="49dc2-166">Jeśli `AuthenticationScheme` nie jest określony, używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="49dc2-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="49dc2-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> jest używany tylko w przypadku kilku konkretnych ścieżek domyślnie, na przykład ścieżki logowania i ścieżki wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="49dc2-168">Aby uzyskać więcej informacji, zobacz [ Cookie Źródło AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="49dc2-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="49dc2-169">System [ochrony danych](xref:security/data-protection/using-data-protection) ASP.NET Core jest używany do szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="49dc2-170">W przypadku aplikacji hostowanej na wielu komputerach, równoważenia obciążenia między aplikacjami lub korzystania z kolektywu serwerów sieci Web należy [skonfigurować ochronę danych](xref:security/data-protection/configuration/overview) tak, aby używała tego samego dzwonka klucza i identyfikatora aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="49dc2-171">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="49dc2-171">Sign out</span></span>

<span data-ttu-id="49dc2-172">Aby wylogować bieżącego użytkownika i usunąć cookie połączenie, wywołaj <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="49dc2-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="49dc2-173">Jeśli `CookieAuthenticationDefaults.AuthenticationScheme` (lub " Cookie s") nie jest używany jako schemat (na przykład "contoso Cookie "), podaj Schemat używany podczas konfigurowania dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="49dc2-174">W przeciwnym razie używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="49dc2-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="49dc2-175">Serwer nie ma kontroli nad przeglądarką klientów.</span><span class="sxs-lookup"><span data-stu-id="49dc2-175">The server has no control of the clients browser.</span></span> <span data-ttu-id="49dc2-176">Jeśli użytkownik zamknie przeglądarkę lub kartę, serwer nie może wylogować użytkownika.</span><span class="sxs-lookup"><span data-stu-id="49dc2-176">If the user closes the browser or tab, the server cannot sign out the user.</span></span> <span data-ttu-id="49dc2-177">Aby zaimplementować wylogowywanie użytkownika po zamknięciu przeglądarki, należy wykryć, że w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="49dc2-177">To implement signing out the user when the browser is closed, you must detect that with JavaScript.</span></span> <span data-ttu-id="49dc2-178">Wyszukaj ciąg "Jak wykryć zdarzenie zamknięcia karty okna przeglądarki?".</span><span class="sxs-lookup"><span data-stu-id="49dc2-178">Search for "How to Detect Browser Window Tab Close Event?".</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="49dc2-179">Reagowanie na zmiany zaplecza</span><span class="sxs-lookup"><span data-stu-id="49dc2-179">React to back-end changes</span></span>

<span data-ttu-id="49dc2-180">Gdy cookie zostanie utworzony, cookie jest to pojedyncze Źródło tożsamości.</span><span class="sxs-lookup"><span data-stu-id="49dc2-180">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="49dc2-181">Jeśli konto użytkownika jest wyłączone w systemach zaplecza:</span><span class="sxs-lookup"><span data-stu-id="49dc2-181">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="49dc2-182">cookieSystem uwierzytelniania aplikacji kontynuuje przetwarzanie żądań na podstawie uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="49dc2-182">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="49dc2-183">Użytkownik pozostaje zalogowany do aplikacji, o ile uwierzytelnianie cookie jest prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="49dc2-183">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="49dc2-184"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Zdarzenie może służyć do przechwycenia i zastąpienia weryfikacji cookie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="49dc2-184">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="49dc2-185">Sprawdzanie poprawności cookie na każde żądanie zmniejsza ryzyko odwołujący się użytkowników, którzy uzyskują dostęp do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-185">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="49dc2-186">Jedno z metod cookie weryfikacji polega na śledzeniu zmian w bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="49dc2-186">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="49dc2-187">Jeśli baza danych nie została zmieniona od czasu cookie wystawienia użytkownika, nie ma potrzeby ponownego uwierzytelnienia użytkownika, jeśli cookie jest on nadal ważny.</span><span class="sxs-lookup"><span data-stu-id="49dc2-187">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="49dc2-188">W przykładowej aplikacji baza danych jest zaimplementowana w `IUserRepository` i przechowuje `LastChanged` wartość.</span><span class="sxs-lookup"><span data-stu-id="49dc2-188">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="49dc2-189">Gdy użytkownik zostanie zaktualizowany w bazie danych, `LastChanged` wartość jest ustawiana na bieżącą godzinę.</span><span class="sxs-lookup"><span data-stu-id="49dc2-189">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="49dc2-190">Aby unieważnić cookie , kiedy baza danych zmienia się w zależności od `LastChanged` wartości, Utwórz cookie za pomocą `LastChanged` żądania zawierającego bieżącą `LastChanged` wartość z bazy danych:</span><span class="sxs-lookup"><span data-stu-id="49dc2-190">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="49dc2-191">Aby zaimplementować przesłonięcie dla `ValidatePrincipal` zdarzenia, napisz metodę o następującym podpisie w klasie, która pochodzi od <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="49dc2-191">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="49dc2-192">Poniżej przedstawiono przykładową implementację programu `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="49dc2-192">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="49dc2-193">Zarejestruj wystąpienie zdarzeń podczas cookie rejestracji usługi w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="49dc2-193">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="49dc2-194">Podaj [rejestrację usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes) dla swojej `CustomCookieAuthenticationEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="49dc2-194">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="49dc2-195">Rozważ sytuację, w której nazwa użytkownika jest aktualizowana &mdash; decyzją, która nie ma wpływu na zabezpieczenia w jakikolwiek sposób.</span><span class="sxs-lookup"><span data-stu-id="49dc2-195">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="49dc2-196">Jeśli chcesz nieszkodliwej aktualizacji podmiotu zabezpieczeń, wywołaj `context.ReplacePrincipal` i ustaw `context.ShouldRenew` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="49dc2-196">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="49dc2-197">Opisane tutaj podejście jest wyzwalane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-197">The approach described here is triggered on every request.</span></span> <span data-ttu-id="49dc2-198">Sprawdzanie poprawności uwierzytelniania cookie s dla wszystkich użytkowników w każdym żądaniu może spowodować spadek wydajności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-198">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="49dc2-199">Trwałe cookie s</span><span class="sxs-lookup"><span data-stu-id="49dc2-199">Persistent cookies</span></span>

<span data-ttu-id="49dc2-200">Może zaistnieć potrzeba cookie utrwalenia sesji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="49dc2-200">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="49dc2-201">Tę trwałość należy włączyć tylko w przypadku jawnej zgody użytkownika przy użyciu pola wyboru "Zapamiętaj mnie" przy logowaniu lub podobnym mechanizmie.</span><span class="sxs-lookup"><span data-stu-id="49dc2-201">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="49dc2-202">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie za przechodzenie przez zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="49dc2-202">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="49dc2-203">Wszystkie skonfigurowane wcześniej ustawienia wygasania są honorowane.</span><span class="sxs-lookup"><span data-stu-id="49dc2-203">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="49dc2-204">Jeśli cookie wygaśnie, gdy przeglądarka zostanie ZAMKNIĘTA, przeglądarka czyści cookie po jej ponownym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="49dc2-204">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="49dc2-205">Ustaw <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na wartość `true` w <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="49dc2-205">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="49dc2-206">Bezwzględne cookie wygaśnięcie</span><span class="sxs-lookup"><span data-stu-id="49dc2-206">Absolute cookie expiration</span></span>

<span data-ttu-id="49dc2-207">Bezwzględny czas wygaśnięcia można ustawić za pomocą <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="49dc2-207">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="49dc2-208">Aby utworzyć trwały cookie , `IsPersistent` należy również ustawić wartość.</span><span class="sxs-lookup"><span data-stu-id="49dc2-208">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="49dc2-209">W przeciwnym razie cookie jest tworzona przy użyciu okresu istnienia sesji i może wygasnąć przed lub po posiadanym przez niego biletem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-209">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="49dc2-210">Gdy `ExpiresUtc` jest ustawiona, zastępuje wartość <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opcji <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , jeśli jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="49dc2-210">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="49dc2-211">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="49dc2-211">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="49dc2-212">Ignoruje to wszystkie ustawienia wygasania, które zostały wcześniej skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="49dc2-212">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="49dc2-213">ASP.NET Core Identity jest kompletnym, w pełni funkcjonalnym dostawcą uwierzytelniania na potrzeby tworzenia i obsługi logowania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-213">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="49dc2-214">cookieNie można jednak użyć dostawcy uwierzytelniania opartego na usłudze ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="49dc2-214">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="49dc2-215">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="49dc2-215">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="49dc2-216">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49dc2-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="49dc2-217">W celach demonstracyjnych w przykładowej aplikacji konto użytkownika dla hipotetycznego użytkownika, Maria Rodriguez jest stałe do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-217">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="49dc2-218">Zaloguj się przy użyciu adresu **e-mail** `maria.rodriguez@contoso.com` i hasła.</span><span class="sxs-lookup"><span data-stu-id="49dc2-218">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="49dc2-219">Użytkownik jest uwierzytelniany w `AuthenticateUser` metodzie w pliku *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="49dc2-219">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="49dc2-220">W świecie rzeczywistym użytkownik zostanie uwierzytelniony w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="49dc2-220">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="49dc2-221">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="49dc2-221">Configuration</span></span>

<span data-ttu-id="49dc2-222">Jeśli aplikacja nie korzysta z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu w pliku projektu dla elementu [Microsoft. AspNetCore. Authentication. Cookie Pakiet s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="49dc2-222">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="49dc2-223">W `Startup.ConfigureServices` metodzie Utwórz usługę pośredniczącą uwierzytelniania przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod i:</span><span class="sxs-lookup"><span data-stu-id="49dc2-223">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="49dc2-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> przeszedł do `AddAuthentication` Ustawia domyślny schemat uwierzytelniania dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="49dc2-225">`AuthenticationScheme` jest przydatne, gdy istnieje wiele wystąpień cookie uwierzytelniania i chcesz [autoryzować z określonym schematem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="49dc2-225">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="49dc2-226">Ustawienie parametru `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) zapewnia wartość " Cookie s" dla schematu.</span><span class="sxs-lookup"><span data-stu-id="49dc2-226">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="49dc2-227">Można podać dowolną wartość ciągu odróżniającą schemat.</span><span class="sxs-lookup"><span data-stu-id="49dc2-227">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="49dc2-228">Schemat uwierzytelniania aplikacji różni się od cookie schematu uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-228">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="49dc2-229">Gdy cookie schemat uwierzytelniania nie jest dostarczany <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , używa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="49dc2-229">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="49dc2-230">Właściwość uwierzytelnianie cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> jest domyślnie ustawiona na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="49dc2-230">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="49dc2-231">Uwierzytelnianie cookie s jest dozwolone, gdy odwiedzający witrynę nie wyraził zgodę na zbieranie danych.</span><span class="sxs-lookup"><span data-stu-id="49dc2-231">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="49dc2-232">Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="49dc2-232">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="49dc2-233">W `Startup.Configure` metodzie Wywołaj `UseAuthentication` metodę, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia `HttpContext.User` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="49dc2-233">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="49dc2-234">Wywołaj `UseAuthentication` metodę przed wywołaniem `UseMvcWithDefaultRoute` lub `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="49dc2-234">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="49dc2-235"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Klasa służy do konfigurowania opcji dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-235">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="49dc2-236">Ustaw `CookieAuthenticationOptions` w konfiguracji usługi na potrzeby uwierzytelniania w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="49dc2-236">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="49dc2-237">Cookie Oprogramowanie pośredniczące zasad</span><span class="sxs-lookup"><span data-stu-id="49dc2-237">Cookie Policy Middleware</span></span>

<span data-ttu-id="49dc2-238">[ Cookie Oprogramowanie pośredniczące zasad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umożliwia korzystanie z cookie możliwości zasad.</span><span class="sxs-lookup"><span data-stu-id="49dc2-238">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="49dc2-239">Dodanie oprogramowania pośredniczącego do potoku przetwarzania aplikacji jest zgodne z kolejnością, ma &mdash; wpływ tylko na składniki podrzędne zarejestrowane w potoku.</span><span class="sxs-lookup"><span data-stu-id="49dc2-239">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="49dc2-240">Użycie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> zapewniane przez Cookie oprogramowanie pośredniczące zasad do sterowania globalnymi charakterystykami cookie przetwarzania i cookie podłączania do programów obsługi przetwarzania, gdy cookie s są dodawane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="49dc2-240">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="49dc2-241">Wartością domyślną <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> jest `SameSiteMode.Lax` umożliwienie uwierzytelniania OAuth2.</span><span class="sxs-lookup"><span data-stu-id="49dc2-241">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="49dc2-242">Aby ściśle wymuszać zasady dotyczące tej samej lokacji `SameSiteMode.Strict` , należy ustawić `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="49dc2-242">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="49dc2-243">Mimo że to ustawienie powoduje przerwanie OAuth2 i innych schematów uwierzytelniania między źródłami, podnosi poziom cookie zabezpieczeń dla innych typów aplikacji, które nie polegają na przetwarzaniu żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="49dc2-243">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="49dc2-244">CookieUstawienie ustawienia pośredniczące zasad dla programu `MinimumSameSitePolicy` może mieć wpływ na ustawienia `Cookie.SameSite` w `CookieAuthenticationOptions` ustawieniach zgodnie z poniższą macierzą.</span><span class="sxs-lookup"><span data-stu-id="49dc2-244">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="49dc2-245">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="49dc2-245">MinimumSameSitePolicy</span></span> | <span data-ttu-id="49dc2-246">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="49dc2-246">Cookie.SameSite</span></span> | <span data-ttu-id="49dc2-247">Wynik Cookie . Ustawienie SameSite</span><span class="sxs-lookup"><span data-stu-id="49dc2-247">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="49dc2-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-248">SameSiteMode.None</span></span>     | <span data-ttu-id="49dc2-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-249">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-250">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-251">SameSiteMode.Strict</span></span> | <span data-ttu-id="49dc2-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-252">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-253">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-254">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="49dc2-255">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-255">SameSiteMode.Lax</span></span>      | <span data-ttu-id="49dc2-256">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-256">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-257">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-258">SameSiteMode.Strict</span></span> | <span data-ttu-id="49dc2-259">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-259">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-260">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-261">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="49dc2-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-262">SameSiteMode.Strict</span></span>   | <span data-ttu-id="49dc2-263">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="49dc2-263">SameSiteMode.None</span></span><br><span data-ttu-id="49dc2-264">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="49dc2-264">SameSiteMode.Lax</span></span><br><span data-ttu-id="49dc2-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-265">SameSiteMode.Strict</span></span> | <span data-ttu-id="49dc2-266">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-266">SameSiteMode.Strict</span></span><br><span data-ttu-id="49dc2-267">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-267">SameSiteMode.Strict</span></span><br><span data-ttu-id="49dc2-268">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="49dc2-268">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="49dc2-269">Tworzenie uwierzytelniania cookie</span><span class="sxs-lookup"><span data-stu-id="49dc2-269">Create an authentication cookie</span></span>

<span data-ttu-id="49dc2-270">Aby utworzyć cookie Informacje o użytkowniku, konstrukcja a <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="49dc2-270">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="49dc2-271">Informacje o użytkowniku są serializowane i przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="49dc2-271">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="49dc2-272">Utwórz <xref:System.Security.Claims.ClaimsIdentity> z dowolnym wymaganym elementem <xref:System.Security.Claims.Claim> s i Wywołaj, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Aby zalogować użytkownika:</span><span class="sxs-lookup"><span data-stu-id="49dc2-272">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="49dc2-273">`SignInAsync` tworzy zaszyfrowaną cookie i dodaje ją do bieżącej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="49dc2-273">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="49dc2-274">Jeśli `AuthenticationScheme` nie jest określony, używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="49dc2-274">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="49dc2-275">System [ochrony danych](xref:security/data-protection/using-data-protection) ASP.NET Core jest używany do szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-275">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="49dc2-276">W przypadku aplikacji hostowanej na wielu komputerach, równoważenia obciążenia między aplikacjami lub korzystania z kolektywu serwerów sieci Web należy [skonfigurować ochronę danych](xref:security/data-protection/configuration/overview) tak, aby używała tego samego dzwonka klucza i identyfikatora aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-276">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="49dc2-277">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="49dc2-277">Sign out</span></span>

<span data-ttu-id="49dc2-278">Aby wylogować bieżącego użytkownika i usunąć cookie połączenie, wywołaj <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="49dc2-278">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="49dc2-279">Jeśli `CookieAuthenticationDefaults.AuthenticationScheme` (lub " Cookie s") nie jest używany jako schemat (na przykład "contoso Cookie "), podaj Schemat używany podczas konfigurowania dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-279">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="49dc2-280">W przeciwnym razie używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="49dc2-280">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="49dc2-281">Reagowanie na zmiany zaplecza</span><span class="sxs-lookup"><span data-stu-id="49dc2-281">React to back-end changes</span></span>

<span data-ttu-id="49dc2-282">Gdy cookie zostanie utworzony, cookie jest to pojedyncze Źródło tożsamości.</span><span class="sxs-lookup"><span data-stu-id="49dc2-282">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="49dc2-283">Jeśli konto użytkownika jest wyłączone w systemach zaplecza:</span><span class="sxs-lookup"><span data-stu-id="49dc2-283">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="49dc2-284">cookieSystem uwierzytelniania aplikacji kontynuuje przetwarzanie żądań na podstawie uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="49dc2-284">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="49dc2-285">Użytkownik pozostaje zalogowany do aplikacji, o ile uwierzytelnianie cookie jest prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="49dc2-285">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="49dc2-286"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Zdarzenie może służyć do przechwycenia i zastąpienia weryfikacji cookie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="49dc2-286">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="49dc2-287">Sprawdzanie poprawności cookie na każde żądanie zmniejsza ryzyko odwołujący się użytkowników, którzy uzyskują dostęp do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-287">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="49dc2-288">Jedno z metod cookie weryfikacji polega na śledzeniu zmian w bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="49dc2-288">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="49dc2-289">Jeśli baza danych nie została zmieniona od czasu cookie wystawienia użytkownika, nie ma potrzeby ponownego uwierzytelnienia użytkownika, jeśli cookie jest on nadal ważny.</span><span class="sxs-lookup"><span data-stu-id="49dc2-289">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="49dc2-290">W przykładowej aplikacji baza danych jest zaimplementowana w `IUserRepository` i przechowuje `LastChanged` wartość.</span><span class="sxs-lookup"><span data-stu-id="49dc2-290">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="49dc2-291">Gdy użytkownik zostanie zaktualizowany w bazie danych, `LastChanged` wartość jest ustawiana na bieżącą godzinę.</span><span class="sxs-lookup"><span data-stu-id="49dc2-291">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="49dc2-292">Aby unieważnić cookie , kiedy baza danych zmienia się w zależności od `LastChanged` wartości, Utwórz cookie za pomocą `LastChanged` żądania zawierającego bieżącą `LastChanged` wartość z bazy danych:</span><span class="sxs-lookup"><span data-stu-id="49dc2-292">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="49dc2-293">Aby zaimplementować przesłonięcie dla `ValidatePrincipal` zdarzenia, napisz metodę o następującym podpisie w klasie, która pochodzi od <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="49dc2-293">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="49dc2-294">Poniżej przedstawiono przykładową implementację programu `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="49dc2-294">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="49dc2-295">Zarejestruj wystąpienie zdarzeń podczas cookie rejestracji usługi w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="49dc2-295">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="49dc2-296">Podaj [rejestrację usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes) dla swojej `CustomCookieAuthenticationEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="49dc2-296">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="49dc2-297">Rozważ sytuację, w której nazwa użytkownika jest aktualizowana &mdash; decyzją, która nie ma wpływu na zabezpieczenia w jakikolwiek sposób.</span><span class="sxs-lookup"><span data-stu-id="49dc2-297">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="49dc2-298">Jeśli chcesz nieszkodliwej aktualizacji podmiotu zabezpieczeń, wywołaj `context.ReplacePrincipal` i ustaw `context.ShouldRenew` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="49dc2-298">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="49dc2-299">Opisane tutaj podejście jest wyzwalane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-299">The approach described here is triggered on every request.</span></span> <span data-ttu-id="49dc2-300">Sprawdzanie poprawności uwierzytelniania cookie s dla wszystkich użytkowników w każdym żądaniu może spowodować spadek wydajności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49dc2-300">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="49dc2-301">Trwałe cookie s</span><span class="sxs-lookup"><span data-stu-id="49dc2-301">Persistent cookies</span></span>

<span data-ttu-id="49dc2-302">Może zaistnieć potrzeba cookie utrwalenia sesji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="49dc2-302">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="49dc2-303">Tę trwałość należy włączyć tylko w przypadku jawnej zgody użytkownika przy użyciu pola wyboru "Zapamiętaj mnie" przy logowaniu lub podobnym mechanizmie.</span><span class="sxs-lookup"><span data-stu-id="49dc2-303">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="49dc2-304">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie za przechodzenie przez zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="49dc2-304">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="49dc2-305">Wszystkie skonfigurowane wcześniej ustawienia wygasania są honorowane.</span><span class="sxs-lookup"><span data-stu-id="49dc2-305">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="49dc2-306">Jeśli cookie wygaśnie, gdy przeglądarka zostanie ZAMKNIĘTA, przeglądarka czyści cookie po jej ponownym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="49dc2-306">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="49dc2-307">Ustaw <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na wartość `true` w <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="49dc2-307">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="49dc2-308">Bezwzględne cookie wygaśnięcie</span><span class="sxs-lookup"><span data-stu-id="49dc2-308">Absolute cookie expiration</span></span>

<span data-ttu-id="49dc2-309">Bezwzględny czas wygaśnięcia można ustawić za pomocą <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="49dc2-309">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="49dc2-310">Aby utworzyć trwały cookie , `IsPersistent` należy również ustawić wartość.</span><span class="sxs-lookup"><span data-stu-id="49dc2-310">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="49dc2-311">W przeciwnym razie cookie jest tworzona przy użyciu okresu istnienia sesji i może wygasnąć przed lub po posiadanym przez niego biletem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="49dc2-311">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="49dc2-312">Gdy `ExpiresUtc` jest ustawiona, zastępuje wartość <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opcji <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , jeśli jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="49dc2-312">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="49dc2-313">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="49dc2-313">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="49dc2-314">Ignoruje to wszystkie ustawienia wygasania, które zostały wcześniej skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="49dc2-314">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="49dc2-315">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="49dc2-315">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="49dc2-316">Kontrole ról oparte na zasadach</span><span class="sxs-lookup"><span data-stu-id="49dc2-316">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
