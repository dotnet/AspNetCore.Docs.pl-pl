---
title: 'Użyj cookie uwierzytelniania bez ASP.NET Core Identity'
author: rick-anderson
description: 'Dowiedz się, jak używać cookie uwierzytelniania bez ASP.NET Core Identity .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: 'security/authentication/cookie'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061356"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="46674-103">Użyj cookie uwierzytelniania bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="46674-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="46674-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="46674-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46674-105">ASP.NET Core Identity jest kompletnym, w pełni funkcjonalnym dostawcą uwierzytelniania na potrzeby tworzenia i obsługi logowania.</span><span class="sxs-lookup"><span data-stu-id="46674-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="46674-106">cookieNie można jednak użyć dostawcy uwierzytelniania opartego na usłudze ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="46674-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="46674-107">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="46674-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="46674-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="46674-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="46674-109">W celach demonstracyjnych w przykładowej aplikacji konto użytkownika dla hipotetycznego użytkownika, Maria Rodriguez jest stałe do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="46674-110">Zaloguj się przy użyciu adresu **e-mail** `maria.rodriguez@contoso.com` i hasła.</span><span class="sxs-lookup"><span data-stu-id="46674-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="46674-111">Użytkownik jest uwierzytelniany w `AuthenticateUser` metodzie w pliku *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="46674-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="46674-112">W świecie rzeczywistym użytkownik zostanie uwierzytelniony w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="46674-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="46674-113">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="46674-113">Configuration</span></span>

<span data-ttu-id="46674-114">W `Startup.ConfigureServices` metodzie Utwórz usługi pośredniczące uwierzytelniania przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod i:</span><span class="sxs-lookup"><span data-stu-id="46674-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="46674-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> przeszedł do `AddAuthentication` Ustawia domyślny schemat uwierzytelniania dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="46674-116">`AuthenticationScheme` jest przydatne, gdy istnieje wiele wystąpień cookie uwierzytelniania i chcesz [autoryzować z określonym schematem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="46674-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="46674-117">Ustawienie parametru `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) zapewnia wartość " Cookie s" dla schematu.</span><span class="sxs-lookup"><span data-stu-id="46674-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="46674-118">Można podać dowolną wartość ciągu odróżniającą schemat.</span><span class="sxs-lookup"><span data-stu-id="46674-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="46674-119">Schemat uwierzytelniania aplikacji różni się od cookie schematu uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="46674-120">Gdy cookie schemat uwierzytelniania nie jest dostarczany <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , używa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="46674-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="46674-121">Właściwość uwierzytelnianie cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> jest domyślnie ustawiona na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="46674-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="46674-122">Uwierzytelnianie cookie s jest dozwolone, gdy odwiedzający witrynę nie wyraził zgodę na zbieranie danych.</span><span class="sxs-lookup"><span data-stu-id="46674-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="46674-123">Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="46674-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="46674-124">W `Startup.Configure` , wywołaj `UseAuthentication` i, `UseAuthorization` Aby ustawić `HttpContext.User` Właściwość i uruchamiać oprogramowanie pośredniczące autoryzacji dla żądań.</span><span class="sxs-lookup"><span data-stu-id="46674-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="46674-125">Wywołaj `UseAuthentication` `UseAuthorization` metody i przed wywołaniem `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="46674-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="46674-126"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Klasa służy do konfigurowania opcji dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="46674-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="46674-127">Ustaw `CookieAuthenticationOptions` w konfiguracji usługi na potrzeby uwierzytelniania w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="46674-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="46674-128">Cookie Oprogramowanie pośredniczące zasad</span><span class="sxs-lookup"><span data-stu-id="46674-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="46674-129">[ Cookie Oprogramowanie pośredniczące zasad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umożliwia korzystanie z cookie możliwości zasad.</span><span class="sxs-lookup"><span data-stu-id="46674-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="46674-130">Dodanie oprogramowania pośredniczącego do potoku przetwarzania aplikacji jest zgodne z kolejnością, ma &mdash; wpływ tylko na składniki podrzędne zarejestrowane w potoku.</span><span class="sxs-lookup"><span data-stu-id="46674-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="46674-131">Użycie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> zapewniane przez Cookie oprogramowanie pośredniczące zasad do sterowania globalnymi charakterystykami cookie przetwarzania i cookie podłączania do programów obsługi przetwarzania, gdy cookie s są dodawane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="46674-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="46674-132">Wartością domyślną <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> jest `SameSiteMode.Lax` umożliwienie uwierzytelniania OAuth2.</span><span class="sxs-lookup"><span data-stu-id="46674-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="46674-133">Aby ściśle wymuszać zasady dotyczące tej samej lokacji `SameSiteMode.Strict` , należy ustawić `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="46674-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="46674-134">Mimo że to ustawienie powoduje przerwanie OAuth2 i innych schematów uwierzytelniania między źródłami, podnosi poziom cookie zabezpieczeń dla innych typów aplikacji, które nie polegają na przetwarzaniu żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="46674-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="46674-135">CookieUstawienie ustawienia pośredniczące zasad dla programu `MinimumSameSitePolicy` może mieć wpływ na ustawienia `Cookie.SameSite` w `CookieAuthenticationOptions` ustawieniach zgodnie z poniższą macierzą.</span><span class="sxs-lookup"><span data-stu-id="46674-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="46674-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="46674-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="46674-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="46674-137">Cookie.SameSite</span></span> | <span data-ttu-id="46674-138">Wynik Cookie . Ustawienie SameSite</span><span class="sxs-lookup"><span data-stu-id="46674-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="46674-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-139">SameSiteMode.None</span></span>     | <span data-ttu-id="46674-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-140">SameSiteMode.None</span></span><br><span data-ttu-id="46674-141">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="46674-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-143">SameSiteMode.None</span></span><br><span data-ttu-id="46674-144">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="46674-146">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="46674-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-147">SameSiteMode.None</span></span><br><span data-ttu-id="46674-148">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="46674-150">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-151">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="46674-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="46674-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-154">SameSiteMode.None</span></span><br><span data-ttu-id="46674-155">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="46674-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="46674-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="46674-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="46674-160">Tworzenie uwierzytelniania cookie</span><span class="sxs-lookup"><span data-stu-id="46674-160">Create an authentication cookie</span></span>

<span data-ttu-id="46674-161">Aby utworzyć cookie Informacje o użytkowniku, konstrukcja a <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="46674-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="46674-162">Informacje o użytkowniku są serializowane i przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="46674-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="46674-163">Utwórz <xref:System.Security.Claims.ClaimsIdentity> z dowolnym wymaganym elementem <xref:System.Security.Claims.Claim> s i Wywołaj, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Aby zalogować użytkownika:</span><span class="sxs-lookup"><span data-stu-id="46674-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="46674-164">`SignInAsync` tworzy zaszyfrowaną cookie i dodaje ją do bieżącej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46674-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="46674-165">Jeśli `AuthenticationScheme` nie jest określony, używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="46674-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="46674-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> jest używany tylko w przypadku kilku konkretnych ścieżek domyślnie, na przykład ścieżki logowania i ścieżki wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="46674-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="46674-167">Aby uzyskać więcej informacji, zobacz [ Cookie Źródło AuthenticationHandler](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="46674-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="46674-168">System [ochrony danych](xref:security/data-protection/using-data-protection) ASP.NET Core jest używany do szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="46674-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="46674-169">W przypadku aplikacji hostowanej na wielu komputerach, równoważenia obciążenia między aplikacjami lub korzystania z kolektywu serwerów sieci Web należy [skonfigurować ochronę danych](xref:security/data-protection/configuration/overview) tak, aby używała tego samego dzwonka klucza i identyfikatora aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="46674-170">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="46674-170">Sign out</span></span>

<span data-ttu-id="46674-171">Aby wylogować bieżącego użytkownika i usunąć cookie połączenie, wywołaj <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="46674-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="46674-172">Jeśli `CookieAuthenticationDefaults.AuthenticationScheme` (lub " Cookie s") nie jest używany jako schemat (na przykład "contoso Cookie "), podaj Schemat używany podczas konfigurowania dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="46674-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="46674-173">W przeciwnym razie używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="46674-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="46674-174">Gdy przeglądarka zamknie ją automatycznie usuwa na podstawie sesji cookie polecenie s (nietrwałe cookie s), ale żadne cookie s nie są czyszczone po zamknięciu pojedynczej karty.</span><span class="sxs-lookup"><span data-stu-id="46674-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="46674-175">Serwer nie jest powiadamiany o zdarzeniach dotyczących karty lub zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="46674-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="46674-176">Reagowanie na zmiany zaplecza</span><span class="sxs-lookup"><span data-stu-id="46674-176">React to back-end changes</span></span>

<span data-ttu-id="46674-177">Gdy cookie zostanie utworzony, cookie jest to pojedyncze Źródło tożsamości.</span><span class="sxs-lookup"><span data-stu-id="46674-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="46674-178">Jeśli konto użytkownika jest wyłączone w systemach zaplecza:</span><span class="sxs-lookup"><span data-stu-id="46674-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="46674-179">cookieSystem uwierzytelniania aplikacji kontynuuje przetwarzanie żądań na podstawie uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="46674-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="46674-180">Użytkownik pozostaje zalogowany do aplikacji, o ile uwierzytelnianie cookie jest prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="46674-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="46674-181"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Zdarzenie może służyć do przechwycenia i zastąpienia weryfikacji cookie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="46674-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="46674-182">Sprawdzanie poprawności cookie na każde żądanie zmniejsza ryzyko odwołujący się użytkowników, którzy uzyskują dostęp do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="46674-183">Jedno z metod cookie weryfikacji polega na śledzeniu zmian w bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="46674-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="46674-184">Jeśli baza danych nie została zmieniona od czasu cookie wystawienia użytkownika, nie ma potrzeby ponownego uwierzytelnienia użytkownika, jeśli cookie jest on nadal ważny.</span><span class="sxs-lookup"><span data-stu-id="46674-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="46674-185">W przykładowej aplikacji baza danych jest zaimplementowana w `IUserRepository` i przechowuje `LastChanged` wartość.</span><span class="sxs-lookup"><span data-stu-id="46674-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="46674-186">Gdy użytkownik zostanie zaktualizowany w bazie danych, `LastChanged` wartość jest ustawiana na bieżącą godzinę.</span><span class="sxs-lookup"><span data-stu-id="46674-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="46674-187">Aby unieważnić cookie , kiedy baza danych zmienia się w zależności od `LastChanged` wartości, Utwórz cookie za pomocą `LastChanged` żądania zawierającego bieżącą `LastChanged` wartość z bazy danych:</span><span class="sxs-lookup"><span data-stu-id="46674-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="46674-188">Aby zaimplementować przesłonięcie dla `ValidatePrincipal` zdarzenia, napisz metodę o następującym podpisie w klasie, która pochodzi od <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="46674-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="46674-189">Poniżej przedstawiono przykładową implementację programu `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="46674-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="46674-190">Zarejestruj wystąpienie zdarzeń podczas cookie rejestracji usługi w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="46674-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="46674-191">Podaj [rejestrację usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes) dla swojej `CustomCookieAuthenticationEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="46674-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="46674-192">Rozważ sytuację, w której nazwa użytkownika jest aktualizowana &mdash; decyzją, która nie ma wpływu na zabezpieczenia w jakikolwiek sposób.</span><span class="sxs-lookup"><span data-stu-id="46674-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="46674-193">Jeśli chcesz nieszkodliwej aktualizacji podmiotu zabezpieczeń, wywołaj `context.ReplacePrincipal` i ustaw `context.ShouldRenew` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="46674-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="46674-194">Opisane tutaj podejście jest wyzwalane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="46674-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="46674-195">Sprawdzanie poprawności uwierzytelniania cookie s dla wszystkich użytkowników w każdym żądaniu może spowodować spadek wydajności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="46674-196">Trwałe cookie s</span><span class="sxs-lookup"><span data-stu-id="46674-196">Persistent cookies</span></span>

<span data-ttu-id="46674-197">Może zaistnieć potrzeba cookie utrwalenia sesji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="46674-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="46674-198">Tę trwałość należy włączyć tylko w przypadku jawnej zgody użytkownika przy użyciu pola wyboru "Zapamiętaj mnie" przy logowaniu lub podobnym mechanizmie.</span><span class="sxs-lookup"><span data-stu-id="46674-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="46674-199">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie za przechodzenie przez zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="46674-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="46674-200">Wszystkie skonfigurowane wcześniej ustawienia wygasania są honorowane.</span><span class="sxs-lookup"><span data-stu-id="46674-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="46674-201">Jeśli cookie wygaśnie, gdy przeglądarka zostanie ZAMKNIĘTA, przeglądarka czyści cookie po jej ponownym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="46674-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="46674-202">Ustaw <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na wartość `true` w <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="46674-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="46674-203">Bezwzględne cookie wygaśnięcie</span><span class="sxs-lookup"><span data-stu-id="46674-203">Absolute cookie expiration</span></span>

<span data-ttu-id="46674-204">Bezwzględny czas wygaśnięcia można ustawić za pomocą <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="46674-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="46674-205">Aby utworzyć trwały cookie , `IsPersistent` należy również ustawić wartość.</span><span class="sxs-lookup"><span data-stu-id="46674-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="46674-206">W przeciwnym razie cookie jest tworzona przy użyciu okresu istnienia sesji i może wygasnąć przed lub po posiadanym przez niego biletem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="46674-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="46674-207">Gdy `ExpiresUtc` jest ustawiona, zastępuje wartość <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opcji <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , jeśli jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="46674-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="46674-208">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="46674-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="46674-209">Ignoruje to wszystkie ustawienia wygasania, które zostały wcześniej skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="46674-209">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="46674-210">ASP.NET Core Identity jest kompletnym, w pełni funkcjonalnym dostawcą uwierzytelniania na potrzeby tworzenia i obsługi logowania.</span><span class="sxs-lookup"><span data-stu-id="46674-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="46674-211">cookieNie można jednak użyć dostawcy uwierzytelniania opartego na usłudze ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="46674-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="46674-212">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="46674-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="46674-213">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="46674-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="46674-214">W celach demonstracyjnych w przykładowej aplikacji konto użytkownika dla hipotetycznego użytkownika, Maria Rodriguez jest stałe do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="46674-215">Zaloguj się przy użyciu adresu **e-mail** `maria.rodriguez@contoso.com` i hasła.</span><span class="sxs-lookup"><span data-stu-id="46674-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="46674-216">Użytkownik jest uwierzytelniany w `AuthenticateUser` metodzie w pliku *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="46674-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="46674-217">W świecie rzeczywistym użytkownik zostanie uwierzytelniony w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="46674-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="46674-218">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="46674-218">Configuration</span></span>

<span data-ttu-id="46674-219">Jeśli aplikacja nie korzysta z [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), Utwórz odwołanie do pakietu w pliku projektu dla elementu [Microsoft. AspNetCore. Authentication. Cookie Pakiet s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="46674-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="46674-220">W `Startup.ConfigureServices` metodzie Utwórz usługę pośredniczącą uwierzytelniania przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod i:</span><span class="sxs-lookup"><span data-stu-id="46674-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="46674-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> przeszedł do `AddAuthentication` Ustawia domyślny schemat uwierzytelniania dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="46674-222">`AuthenticationScheme` jest przydatne, gdy istnieje wiele wystąpień cookie uwierzytelniania i chcesz [autoryzować z określonym schematem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="46674-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="46674-223">Ustawienie parametru `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) zapewnia wartość " Cookie s" dla schematu.</span><span class="sxs-lookup"><span data-stu-id="46674-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="46674-224">Można podać dowolną wartość ciągu odróżniającą schemat.</span><span class="sxs-lookup"><span data-stu-id="46674-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="46674-225">Schemat uwierzytelniania aplikacji różni się od cookie schematu uwierzytelniania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="46674-226">Gdy cookie schemat uwierzytelniania nie jest dostarczany <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , używa `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="46674-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="46674-227">Właściwość uwierzytelnianie cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> jest domyślnie ustawiona na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="46674-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="46674-228">Uwierzytelnianie cookie s jest dozwolone, gdy odwiedzający witrynę nie wyraził zgodę na zbieranie danych.</span><span class="sxs-lookup"><span data-stu-id="46674-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="46674-229">Aby uzyskać więcej informacji, zobacz <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="46674-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="46674-230">W `Startup.Configure` metodzie Wywołaj `UseAuthentication` metodę, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia `HttpContext.User` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="46674-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="46674-231">Wywołaj `UseAuthentication` metodę przed wywołaniem `UseMvcWithDefaultRoute` lub `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="46674-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="46674-232"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Klasa służy do konfigurowania opcji dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="46674-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="46674-233">Ustaw `CookieAuthenticationOptions` w konfiguracji usługi na potrzeby uwierzytelniania w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="46674-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="46674-234">Cookie Oprogramowanie pośredniczące zasad</span><span class="sxs-lookup"><span data-stu-id="46674-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="46674-235">[ Cookie Oprogramowanie pośredniczące zasad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umożliwia korzystanie z cookie możliwości zasad.</span><span class="sxs-lookup"><span data-stu-id="46674-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="46674-236">Dodanie oprogramowania pośredniczącego do potoku przetwarzania aplikacji jest zgodne z kolejnością, ma &mdash; wpływ tylko na składniki podrzędne zarejestrowane w potoku.</span><span class="sxs-lookup"><span data-stu-id="46674-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="46674-237">Użycie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> zapewniane przez Cookie oprogramowanie pośredniczące zasad do sterowania globalnymi charakterystykami cookie przetwarzania i cookie podłączania do programów obsługi przetwarzania, gdy cookie s są dodawane lub usuwane.</span><span class="sxs-lookup"><span data-stu-id="46674-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="46674-238">Wartością domyślną <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> jest `SameSiteMode.Lax` umożliwienie uwierzytelniania OAuth2.</span><span class="sxs-lookup"><span data-stu-id="46674-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="46674-239">Aby ściśle wymuszać zasady dotyczące tej samej lokacji `SameSiteMode.Strict` , należy ustawić `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="46674-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="46674-240">Mimo że to ustawienie powoduje przerwanie OAuth2 i innych schematów uwierzytelniania między źródłami, podnosi poziom cookie zabezpieczeń dla innych typów aplikacji, które nie polegają na przetwarzaniu żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="46674-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="46674-241">CookieUstawienie ustawienia pośredniczące zasad dla programu `MinimumSameSitePolicy` może mieć wpływ na ustawienia `Cookie.SameSite` w `CookieAuthenticationOptions` ustawieniach zgodnie z poniższą macierzą.</span><span class="sxs-lookup"><span data-stu-id="46674-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="46674-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="46674-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="46674-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="46674-243">Cookie.SameSite</span></span> | <span data-ttu-id="46674-244">Wynik Cookie . Ustawienie SameSite</span><span class="sxs-lookup"><span data-stu-id="46674-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="46674-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-245">SameSiteMode.None</span></span>     | <span data-ttu-id="46674-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-246">SameSiteMode.None</span></span><br><span data-ttu-id="46674-247">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="46674-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-249">SameSiteMode.None</span></span><br><span data-ttu-id="46674-250">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="46674-252">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="46674-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-253">SameSiteMode.None</span></span><br><span data-ttu-id="46674-254">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="46674-256">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-257">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="46674-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="46674-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="46674-260">SameSiteMode.None</span></span><br><span data-ttu-id="46674-261">SameSiteMode. swobodny</span><span class="sxs-lookup"><span data-stu-id="46674-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="46674-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="46674-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="46674-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="46674-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="46674-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="46674-266">Tworzenie uwierzytelniania cookie</span><span class="sxs-lookup"><span data-stu-id="46674-266">Create an authentication cookie</span></span>

<span data-ttu-id="46674-267">Aby utworzyć cookie Informacje o użytkowniku, konstrukcja a <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="46674-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="46674-268">Informacje o użytkowniku są serializowane i przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="46674-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="46674-269">Utwórz <xref:System.Security.Claims.ClaimsIdentity> z dowolnym wymaganym elementem <xref:System.Security.Claims.Claim> s i Wywołaj, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Aby zalogować użytkownika:</span><span class="sxs-lookup"><span data-stu-id="46674-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="46674-270">`SignInAsync` tworzy zaszyfrowaną cookie i dodaje ją do bieżącej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="46674-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="46674-271">Jeśli `AuthenticationScheme` nie jest określony, używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="46674-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="46674-272">System [ochrony danych](xref:security/data-protection/using-data-protection) ASP.NET Core jest używany do szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="46674-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="46674-273">W przypadku aplikacji hostowanej na wielu komputerach, równoważenia obciążenia między aplikacjami lub korzystania z kolektywu serwerów sieci Web należy [skonfigurować ochronę danych](xref:security/data-protection/configuration/overview) tak, aby używała tego samego dzwonka klucza i identyfikatora aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="46674-274">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="46674-274">Sign out</span></span>

<span data-ttu-id="46674-275">Aby wylogować bieżącego użytkownika i usunąć cookie połączenie, wywołaj <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="46674-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="46674-276">Jeśli `CookieAuthenticationDefaults.AuthenticationScheme` (lub " Cookie s") nie jest używany jako schemat (na przykład "contoso Cookie "), podaj Schemat używany podczas konfigurowania dostawcy uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="46674-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="46674-277">W przeciwnym razie używany jest domyślny schemat.</span><span class="sxs-lookup"><span data-stu-id="46674-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="46674-278">Reagowanie na zmiany zaplecza</span><span class="sxs-lookup"><span data-stu-id="46674-278">React to back-end changes</span></span>

<span data-ttu-id="46674-279">Gdy cookie zostanie utworzony, cookie jest to pojedyncze Źródło tożsamości.</span><span class="sxs-lookup"><span data-stu-id="46674-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="46674-280">Jeśli konto użytkownika jest wyłączone w systemach zaplecza:</span><span class="sxs-lookup"><span data-stu-id="46674-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="46674-281">cookieSystem uwierzytelniania aplikacji kontynuuje przetwarzanie żądań na podstawie uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="46674-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="46674-282">Użytkownik pozostaje zalogowany do aplikacji, o ile uwierzytelnianie cookie jest prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="46674-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="46674-283"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Zdarzenie może służyć do przechwycenia i zastąpienia weryfikacji cookie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="46674-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="46674-284">Sprawdzanie poprawności cookie na każde żądanie zmniejsza ryzyko odwołujący się użytkowników, którzy uzyskują dostęp do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="46674-285">Jedno z metod cookie weryfikacji polega na śledzeniu zmian w bazie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="46674-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="46674-286">Jeśli baza danych nie została zmieniona od czasu cookie wystawienia użytkownika, nie ma potrzeby ponownego uwierzytelnienia użytkownika, jeśli cookie jest on nadal ważny.</span><span class="sxs-lookup"><span data-stu-id="46674-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="46674-287">W przykładowej aplikacji baza danych jest zaimplementowana w `IUserRepository` i przechowuje `LastChanged` wartość.</span><span class="sxs-lookup"><span data-stu-id="46674-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="46674-288">Gdy użytkownik zostanie zaktualizowany w bazie danych, `LastChanged` wartość jest ustawiana na bieżącą godzinę.</span><span class="sxs-lookup"><span data-stu-id="46674-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="46674-289">Aby unieważnić cookie , kiedy baza danych zmienia się w zależności od `LastChanged` wartości, Utwórz cookie za pomocą `LastChanged` żądania zawierającego bieżącą `LastChanged` wartość z bazy danych:</span><span class="sxs-lookup"><span data-stu-id="46674-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="46674-290">Aby zaimplementować przesłonięcie dla `ValidatePrincipal` zdarzenia, napisz metodę o następującym podpisie w klasie, która pochodzi od <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="46674-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="46674-291">Poniżej przedstawiono przykładową implementację programu `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="46674-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="46674-292">Zarejestruj wystąpienie zdarzeń podczas cookie rejestracji usługi w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="46674-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="46674-293">Podaj [rejestrację usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes) dla swojej `CustomCookieAuthenticationEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="46674-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="46674-294">Rozważ sytuację, w której nazwa użytkownika jest aktualizowana &mdash; decyzją, która nie ma wpływu na zabezpieczenia w jakikolwiek sposób.</span><span class="sxs-lookup"><span data-stu-id="46674-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="46674-295">Jeśli chcesz nieszkodliwej aktualizacji podmiotu zabezpieczeń, wywołaj `context.ReplacePrincipal` i ustaw `context.ShouldRenew` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="46674-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="46674-296">Opisane tutaj podejście jest wyzwalane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="46674-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="46674-297">Sprawdzanie poprawności uwierzytelniania cookie s dla wszystkich użytkowników w każdym żądaniu może spowodować spadek wydajności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="46674-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="46674-298">Trwałe cookie s</span><span class="sxs-lookup"><span data-stu-id="46674-298">Persistent cookies</span></span>

<span data-ttu-id="46674-299">Może zaistnieć potrzeba cookie utrwalenia sesji w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="46674-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="46674-300">Tę trwałość należy włączyć tylko w przypadku jawnej zgody użytkownika przy użyciu pola wyboru "Zapamiętaj mnie" przy logowaniu lub podobnym mechanizmie.</span><span class="sxs-lookup"><span data-stu-id="46674-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="46674-301">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie za przechodzenie przez zamknięcia przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="46674-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="46674-302">Wszystkie skonfigurowane wcześniej ustawienia wygasania są honorowane.</span><span class="sxs-lookup"><span data-stu-id="46674-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="46674-303">Jeśli cookie wygaśnie, gdy przeglądarka zostanie ZAMKNIĘTA, przeglądarka czyści cookie po jej ponownym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="46674-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="46674-304">Ustaw <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na wartość `true` w <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="46674-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="46674-305">Bezwzględne cookie wygaśnięcie</span><span class="sxs-lookup"><span data-stu-id="46674-305">Absolute cookie expiration</span></span>

<span data-ttu-id="46674-306">Bezwzględny czas wygaśnięcia można ustawić za pomocą <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="46674-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="46674-307">Aby utworzyć trwały cookie , `IsPersistent` należy również ustawić wartość.</span><span class="sxs-lookup"><span data-stu-id="46674-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="46674-308">W przeciwnym razie cookie jest tworzona przy użyciu okresu istnienia sesji i może wygasnąć przed lub po posiadanym przez niego biletem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="46674-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="46674-309">Gdy `ExpiresUtc` jest ustawiona, zastępuje wartość <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opcji <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , jeśli jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="46674-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="46674-310">Poniższy fragment kodu tworzy tożsamość i odpowiada cookie na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="46674-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="46674-311">Ignoruje to wszystkie ustawienia wygasania, które zostały wcześniej skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="46674-311">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="46674-312">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="46674-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="46674-313">Kontrole ról oparte na zasadach</span><span class="sxs-lookup"><span data-stu-id="46674-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
