---
title: 'Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym bez :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'Wyjaśnienie dotyczące korzystania z usługi Facebook, Google, Twitter itp. i uwierzytelniania użytkownika konta bez :::no-loc(ASP.NET Core Identity)::: .'
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060290"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="7134c-103">Korzystanie z uwierzytelniania przy użyciu dostawcy logowania społecznego bez :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="7134c-103">Use social sign-in provider authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="7134c-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7134c-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7134c-105"><xref:security/authentication/social/index> Opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7134c-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="7134c-106">Podejście opisane w tym temacie obejmuje :::no-loc(ASP.NET Core Identity)::: jako dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7134c-106">The approach described in that topic includes :::no-loc(ASP.NET Core Identity)::: as an authentication provider.</span></span>

<span data-ttu-id="7134c-107">Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="7134c-107">This sample demonstrates how to use an external authentication provider **without** :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="7134c-108">Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji programu :::no-loc(ASP.NET Core Identity)::: , ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="7134c-108">This is useful for apps that don't require all of the features of :::no-loc(ASP.NET Core Identity):::, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="7134c-109">Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7134c-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="7134c-110">Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google.</span><span class="sxs-lookup"><span data-stu-id="7134c-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="7134c-111">Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="7134c-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="7134c-112">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="7134c-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7134c-113">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="7134c-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7134c-114">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="7134c-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7134c-115">Inni dostawcy</span><span class="sxs-lookup"><span data-stu-id="7134c-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="7134c-116">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="7134c-116">Configuration</span></span>

<span data-ttu-id="7134c-117">W tej `ConfigureServices` metodzie Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> metod, i <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="7134c-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="7134c-118">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ustawiające aplikację <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="7134c-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="7134c-119">`DefaultScheme`Jest domyślnym schematem używanym przez następujące `HttpContext` metody rozszerzenia uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="7134c-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="7134c-120">Ustawienie aplikacji `DefaultScheme` na [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (" :::no-loc(Cookie)::: s") spowoduje skonfigurowanie aplikacji do użycia :::no-loc(Cookie)::: jako schemat domyślny dla tych metod rozszerzających.</span><span class="sxs-lookup"><span data-stu-id="7134c-120">Setting the app's `DefaultScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (":::no-loc(Cookie):::s") configures the app to use :::no-loc(Cookie):::s as the default scheme for these extension methods.</span></span> <span data-ttu-id="7134c-121">Ustawienie aplikacji <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do korzystania z usługi Google jako domyślnego schematu dla wywołań usługi `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="7134c-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="7134c-122">`DefaultChallengeScheme` zastąpień `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="7134c-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="7134c-123">Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> , aby uzyskać dodatkowe właściwości, które przesłonić, `DefaultScheme` gdy ustawione.</span><span class="sxs-lookup"><span data-stu-id="7134c-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="7134c-124">W `Startup.Configure` , wywołaj `UseAuthentication` i `UseAuthorization` między wywołaniem `UseRouting` i `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="7134c-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="7134c-125">Ustawia `HttpContext.User` Właściwość i uruchamia oprogramowanie pośredniczące autoryzacji dla żądań:</span><span class="sxs-lookup"><span data-stu-id="7134c-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="7134c-126">Aby dowiedzieć się więcej o schematach uwierzytelniania, zobacz [pojęcia związane z uwierzytelnianiem](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="7134c-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="7134c-127">Aby dowiedzieć się więcej o :::no-loc(cookie)::: uwierzytelnianiu, zobacz <xref:security/authentication/:::no-loc(cookie):::> .</span><span class="sxs-lookup"><span data-stu-id="7134c-127">To learn more about :::no-loc(cookie)::: authentication, see <xref:security/authentication/:::no-loc(cookie):::>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="7134c-128">Zastosuj autoryzację</span><span class="sxs-lookup"><span data-stu-id="7134c-128">Apply authorization</span></span>

<span data-ttu-id="7134c-129">Przetestuj konfigurację uwierzytelniania aplikacji, stosując `AuthorizeAttribute` atrybut do kontrolera, akcji lub strony.</span><span class="sxs-lookup"><span data-stu-id="7134c-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="7134c-130">Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:</span><span class="sxs-lookup"><span data-stu-id="7134c-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="7134c-131">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="7134c-131">Sign out</span></span>

<span data-ttu-id="7134c-132">Aby wylogować bieżącego użytkownika i usunąć jego :::no-loc(cookie)::: , wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="7134c-132">To sign out the current user and delete their :::no-loc(cookie):::, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="7134c-133">Poniższy kod dodaje `Logout` procedurę obsługi strony do strony *indeks* :</span><span class="sxs-lookup"><span data-stu-id="7134c-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="7134c-134">Zwróć uwagę, że wywołanie metody nie `SignOutAsync` określa schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7134c-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="7134c-135">Aplikacja `DefaultScheme` `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.</span><span class="sxs-lookup"><span data-stu-id="7134c-135">The app's `DefaultScheme` of `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7134c-136">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7134c-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7134c-137"><xref:security/authentication/social/index> Opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7134c-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="7134c-138">Podejście opisane w tym temacie obejmuje :::no-loc(ASP.NET Core Identity)::: jako dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7134c-138">The approach described in that topic includes :::no-loc(ASP.NET Core Identity)::: as an authentication provider.</span></span>

<span data-ttu-id="7134c-139">Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="7134c-139">This sample demonstrates how to use an external authentication provider **without** :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="7134c-140">Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji programu :::no-loc(ASP.NET Core Identity)::: , ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="7134c-140">This is useful for apps that don't require all of the features of :::no-loc(ASP.NET Core Identity):::, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="7134c-141">Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7134c-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="7134c-142">Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google.</span><span class="sxs-lookup"><span data-stu-id="7134c-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="7134c-143">Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="7134c-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="7134c-144">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="7134c-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7134c-145">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="7134c-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7134c-146">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="7134c-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7134c-147">Inni dostawcy</span><span class="sxs-lookup"><span data-stu-id="7134c-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="7134c-148">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="7134c-148">Configuration</span></span>

<span data-ttu-id="7134c-149">W tej `ConfigureServices` metodzie Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu `AddAuthentication` `Add:::no-loc(Cookie):::` metod, i `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="7134c-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `Add:::no-loc(Cookie):::`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="7134c-150">Wywołanie metody [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) ustawia [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7134c-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="7134c-151">`DefaultScheme`Jest domyślnym schematem używanym przez następujące `HttpContext` metody rozszerzenia uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="7134c-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="7134c-152">Ustawienie aplikacji `DefaultScheme` na [ :::no-loc(Cookie)::: AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (" :::no-loc(Cookie)::: s") spowoduje skonfigurowanie aplikacji do użycia :::no-loc(Cookie)::: jako schemat domyślny dla tych metod rozszerzających.</span><span class="sxs-lookup"><span data-stu-id="7134c-152">Setting the app's `DefaultScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (":::no-loc(Cookie):::s") configures the app to use :::no-loc(Cookie):::s as the default scheme for these extension methods.</span></span> <span data-ttu-id="7134c-153">Ustawienie aplikacji <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do korzystania z usługi Google jako domyślnego schematu dla wywołań usługi `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="7134c-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="7134c-154">`DefaultChallengeScheme` zastąpień `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="7134c-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="7134c-155">Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> , aby uzyskać dodatkowe właściwości, które przesłonić, `DefaultScheme` gdy ustawione.</span><span class="sxs-lookup"><span data-stu-id="7134c-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="7134c-156">W `Configure` metodzie Wywołaj `UseAuthentication` metodę, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia `HttpContext.User` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="7134c-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="7134c-157">Wywołaj `UseAuthentication` metodę przed wywołaniem `UseMvcWithDefaultRoute` lub `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="7134c-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="7134c-158">Aby dowiedzieć się więcej o schematach uwierzytelniania, zobacz [pojęcia związane z uwierzytelnianiem](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="7134c-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="7134c-159">Aby dowiedzieć się więcej o :::no-loc(cookie)::: uwierzytelnianiu, zobacz <xref:security/authentication/:::no-loc(cookie):::> .</span><span class="sxs-lookup"><span data-stu-id="7134c-159">To learn more about :::no-loc(cookie)::: authentication, see <xref:security/authentication/:::no-loc(cookie):::>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="7134c-160">Zastosuj autoryzację</span><span class="sxs-lookup"><span data-stu-id="7134c-160">Apply authorization</span></span>

<span data-ttu-id="7134c-161">Przetestuj konfigurację uwierzytelniania aplikacji, stosując `AuthorizeAttribute` atrybut do kontrolera, akcji lub strony.</span><span class="sxs-lookup"><span data-stu-id="7134c-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="7134c-162">Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:</span><span class="sxs-lookup"><span data-stu-id="7134c-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="7134c-163">Wyloguj się</span><span class="sxs-lookup"><span data-stu-id="7134c-163">Sign out</span></span>

<span data-ttu-id="7134c-164">Aby wylogować bieżącego użytkownika i usunąć jego :::no-loc(cookie)::: , wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="7134c-164">To sign out the current user and delete their :::no-loc(cookie):::, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="7134c-165">Poniższy kod dodaje `Logout` procedurę obsługi strony do strony *indeks* :</span><span class="sxs-lookup"><span data-stu-id="7134c-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="7134c-166">Zwróć uwagę, że wywołanie metody nie `SignOutAsync` określa schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7134c-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="7134c-167">Aplikacja `DefaultScheme` `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.</span><span class="sxs-lookup"><span data-stu-id="7134c-167">The app's `DefaultScheme` of `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7134c-168">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7134c-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
