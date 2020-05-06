---
title: Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym bez ASP.NET CoreIdentity
author: rick-anderson
description: Wyjaśnienie dotyczące korzystania z usługi Facebook, Google, Twitter itp. i uwierzytelniania użytkownika konta bez IdentityASP.NET Core.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775742"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="6f67d-103">Korzystanie z uwierzytelniania przy użyciu dostawcy logowania społecznego bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6f67d-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="6f67d-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f67d-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f67d-105"><xref:security/authentication/social/index>Opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6f67d-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="6f67d-106">Podejście opisane w tym temacie zawiera ASP.NET Core Identity jako dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6f67d-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="6f67d-107">Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="6f67d-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="6f67d-108">Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji ASP.NET Core Identity, ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="6f67d-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="6f67d-109">Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="6f67d-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="6f67d-110">Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google.</span><span class="sxs-lookup"><span data-stu-id="6f67d-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="6f67d-111">Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="6f67d-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="6f67d-112">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="6f67d-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6f67d-113">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="6f67d-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6f67d-114">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="6f67d-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6f67d-115">Inni dostawcy</span><span class="sxs-lookup"><span data-stu-id="6f67d-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="6f67d-116">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="6f67d-116">Configuration</span></span>

<span data-ttu-id="6f67d-117">W tej `ConfigureServices` metodzie Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>i: <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*></span><span class="sxs-lookup"><span data-stu-id="6f67d-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="6f67d-118">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ustawiające aplikację <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span><span class="sxs-lookup"><span data-stu-id="6f67d-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="6f67d-119">`DefaultScheme` Jest domyślnym schematem używanym przez następujące `HttpContext` metody rozszerzenia uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="6f67d-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="6f67d-120">Ustawienie aplikacji `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("pliki cookie") spowoduje skonfigurowanie aplikacji do używania plików cookie jako schematu domyślnego dla tych metod rozszerzających.</span><span class="sxs-lookup"><span data-stu-id="6f67d-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="6f67d-121">Ustawienie aplikacji <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do korzystania z usługi Google jako domyślnego schematu dla wywołań usługi `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="6f67d-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="6f67d-122">`DefaultChallengeScheme`zastąpień `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="6f67d-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="6f67d-123">Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> , aby uzyskać dodatkowe właściwości `DefaultScheme` , które przesłonić, gdy ustawione.</span><span class="sxs-lookup"><span data-stu-id="6f67d-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="6f67d-124">W `Startup.Configure`, `UseAuthentication` Wywołaj `UseAuthorization` i między `UseRouting` wywołaniem i `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="6f67d-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="6f67d-125">Ustawia `HttpContext.User` Właściwość i uruchamia oprogramowanie pośredniczące autoryzacji dla żądań:</span><span class="sxs-lookup"><span data-stu-id="6f67d-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="6f67d-126">Aby dowiedzieć się więcej o schematach uwierzytelniania, zobacz [pojęcia związane z uwierzytelnianiem](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="6f67d-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="6f67d-127">Aby dowiedzieć się więcej o uwierzytelnianiu <xref:security/authentication/cookie>plików cookie, zobacz.</span><span class="sxs-lookup"><span data-stu-id="6f67d-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="6f67d-128">Zastosuj autoryzację</span><span class="sxs-lookup"><span data-stu-id="6f67d-128">Apply authorization</span></span>

<span data-ttu-id="6f67d-129">Przetestuj konfigurację uwierzytelniania aplikacji, stosując `AuthorizeAttribute` atrybut do kontrolera, akcji lub strony.</span><span class="sxs-lookup"><span data-stu-id="6f67d-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="6f67d-130">Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:</span><span class="sxs-lookup"><span data-stu-id="6f67d-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="6f67d-131">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="6f67d-131">Sign out</span></span>

<span data-ttu-id="6f67d-132">Aby wylogować bieżącego użytkownika i usunąć jego plik cookie, wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="6f67d-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="6f67d-133">Poniższy kod dodaje procedurę obsługi `Logout` strony do strony *indeks* :</span><span class="sxs-lookup"><span data-stu-id="6f67d-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="6f67d-134">Zwróć uwagę, że wywołanie `SignOutAsync` metody nie określa schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6f67d-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="6f67d-135">Aplikacja `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.</span><span class="sxs-lookup"><span data-stu-id="6f67d-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f67d-136">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6f67d-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f67d-137"><xref:security/authentication/social/index>Opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6f67d-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="6f67d-138">Podejście opisane w tym temacie zawiera ASP.NET Core Identity jako dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6f67d-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="6f67d-139">Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="6f67d-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="6f67d-140">Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji ASP.NET Core Identity, ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="6f67d-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="6f67d-141">Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="6f67d-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="6f67d-142">Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google.</span><span class="sxs-lookup"><span data-stu-id="6f67d-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="6f67d-143">Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="6f67d-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="6f67d-144">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="6f67d-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6f67d-145">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="6f67d-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6f67d-146">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="6f67d-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6f67d-147">Inni dostawcy</span><span class="sxs-lookup"><span data-stu-id="6f67d-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="6f67d-148">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="6f67d-148">Configuration</span></span>

<span data-ttu-id="6f67d-149">W tej `ConfigureServices` metodzie Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu metod `AddAuthentication`, `AddCookie`i: `AddGoogle`</span><span class="sxs-lookup"><span data-stu-id="6f67d-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="6f67d-150">Wywołanie metody [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) ustawia [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6f67d-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="6f67d-151">`DefaultScheme` Jest domyślnym schematem używanym przez następujące `HttpContext` metody rozszerzenia uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="6f67d-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="6f67d-152">Ustawienie aplikacji `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("pliki cookie") spowoduje skonfigurowanie aplikacji do używania plików cookie jako schematu domyślnego dla tych metod rozszerzających.</span><span class="sxs-lookup"><span data-stu-id="6f67d-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="6f67d-153">Ustawienie aplikacji <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do korzystania z usługi Google jako domyślnego schematu dla wywołań usługi `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="6f67d-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="6f67d-154">`DefaultChallengeScheme`zastąpień `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="6f67d-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="6f67d-155">Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> , aby uzyskać dodatkowe właściwości `DefaultScheme` , które przesłonić, gdy ustawione.</span><span class="sxs-lookup"><span data-stu-id="6f67d-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="6f67d-156">W `Configure` metodzie Wywołaj `UseAuthentication` metodę, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia `HttpContext.User` właściwość.</span><span class="sxs-lookup"><span data-stu-id="6f67d-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="6f67d-157">Wywołaj `UseAuthentication` metodę przed `UseMvcWithDefaultRoute` wywołaniem `UseMvc`lub:</span><span class="sxs-lookup"><span data-stu-id="6f67d-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="6f67d-158">Aby dowiedzieć się więcej o schematach uwierzytelniania, zobacz [pojęcia związane z uwierzytelnianiem](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="6f67d-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="6f67d-159">Aby dowiedzieć się więcej o uwierzytelnianiu <xref:security/authentication/cookie>plików cookie, zobacz.</span><span class="sxs-lookup"><span data-stu-id="6f67d-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="6f67d-160">Zastosuj autoryzację</span><span class="sxs-lookup"><span data-stu-id="6f67d-160">Apply authorization</span></span>

<span data-ttu-id="6f67d-161">Przetestuj konfigurację uwierzytelniania aplikacji, stosując `AuthorizeAttribute` atrybut do kontrolera, akcji lub strony.</span><span class="sxs-lookup"><span data-stu-id="6f67d-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="6f67d-162">Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:</span><span class="sxs-lookup"><span data-stu-id="6f67d-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="6f67d-163">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="6f67d-163">Sign out</span></span>

<span data-ttu-id="6f67d-164">Aby wylogować bieżącego użytkownika i usunąć jego plik cookie, wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="6f67d-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="6f67d-165">Poniższy kod dodaje procedurę obsługi `Logout` strony do strony *indeks* :</span><span class="sxs-lookup"><span data-stu-id="6f67d-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="6f67d-166">Zwróć uwagę, że wywołanie `SignOutAsync` metody nie określa schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6f67d-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="6f67d-167">Aplikacja `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.</span><span class="sxs-lookup"><span data-stu-id="6f67d-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f67d-168">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6f67d-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
