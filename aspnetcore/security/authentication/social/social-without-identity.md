---
title: Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym bez ASP.NET CoreIdentity
author: rick-anderson
description: Wyjaśnienie dotyczące korzystania z usługi Facebook, Google, Twitter itp. i uwierzytelniania użytkownika konta bez ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406046"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Korzystanie z uwierzytelniania przy użyciu dostawcy logowania społecznego bez ASP.NET CoreIdentity

Autorzy [Kirka Larkin](https://twitter.com/serpent5) i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>Opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania. Podejście opisane w tym temacie zawiera ASP.NET Core Identity jako dostawcę uwierzytelniania.

Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** ASP.NET Core Identity . Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji ASP.NET Core Identity , ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.

Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników. Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google. Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:

* [Uwierzytelnianie przy użyciu usługi Facebook](xref:security/authentication/facebook-logins)
* [Uwierzytelnianie firmy Microsoft](xref:security/authentication/microsoft-logins)
* [Uwierzytelnianie przy użyciu usługi Twitter](xref:security/authentication/twitter-logins)
* [Inni dostawcy](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguracja

W tej `ConfigureServices` metodzie Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod, i <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ustawiające aplikację <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . `DefaultScheme`Jest domyślnym schematem używanym przez następujące `HttpContext` metody rozszerzenia uwierzytelniania:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Ustawienie aplikacji `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("pliki cookie") spowoduje skonfigurowanie aplikacji do używania plików cookie jako schematu domyślnego dla tych metod rozszerzających. Ustawienie aplikacji <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do korzystania z usługi Google jako domyślnego schematu dla wywołań usługi `ChallengeAsync` . `DefaultChallengeScheme`zastąpień `DefaultScheme` . Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> , aby uzyskać dodatkowe właściwości, które przesłonić, `DefaultScheme` gdy ustawione.

W `Startup.Configure` , wywołaj `UseAuthentication` i `UseAuthorization` między wywołaniem `UseRouting` i `UseEndpoints` . Ustawia `HttpContext.User` Właściwość i uruchamia oprogramowanie pośredniczące autoryzacji dla żądań:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Aby dowiedzieć się więcej o schematach uwierzytelniania, zobacz [pojęcia związane z uwierzytelnianiem](xref:security/authentication/index#authentication-concepts). Aby dowiedzieć się więcej o uwierzytelnianiu plików cookie, zobacz <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Zastosuj autoryzację

Przetestuj konfigurację uwierzytelniania aplikacji, stosując `AuthorizeAttribute` atrybut do kontrolera, akcji lub strony. Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Wyloguj się

Aby wylogować bieżącego użytkownika i usunąć jego plik cookie, wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Poniższy kod dodaje `Logout` procedurę obsługi strony do strony *indeks* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Zwróć uwagę, że wywołanie metody nie `SignOutAsync` określa schematu uwierzytelniania. Aplikacja `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>Opisuje, jak umożliwić użytkownikom logowanie się przy użyciu protokołu OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania. Podejście opisane w tym temacie zawiera ASP.NET Core Identity jako dostawcę uwierzytelniania.

Ten przykład pokazuje, jak używać zewnętrznego dostawcy uwierzytelniania **bez** ASP.NET Core Identity . Jest to przydatne w przypadku aplikacji, które nie wymagają wszystkich funkcji ASP.NET Core Identity , ale nadal wymagają integracji z zaufanym dostawcą uwierzytelniania zewnętrznego.

Ten przykład używa [uwierzytelniania Google](xref:security/authentication/google-logins) do uwierzytelniania użytkowników. Korzystanie z usługi Google Authentication przenosi wiele złożoności zarządzania procesem logowania do usługi Google. Aby zintegrować z innym zewnętrznym dostawcą uwierzytelniania, zapoznaj się z następującymi tematami:

* [Uwierzytelnianie przy użyciu usługi Facebook](xref:security/authentication/facebook-logins)
* [Uwierzytelnianie firmy Microsoft](xref:security/authentication/microsoft-logins)
* [Uwierzytelnianie przy użyciu usługi Twitter](xref:security/authentication/twitter-logins)
* [Inni dostawcy](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguracja

W tej `ConfigureServices` metodzie Skonfiguruj schematy uwierzytelniania aplikacji przy użyciu `AddAuthentication` `AddCookie` metod, i `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Wywołanie metody [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) ustawia [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikacji. `DefaultScheme`Jest domyślnym schematem używanym przez następujące `HttpContext` metody rozszerzenia uwierzytelniania:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Ustawienie aplikacji `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("pliki cookie") spowoduje skonfigurowanie aplikacji do używania plików cookie jako schematu domyślnego dla tych metod rozszerzających. Ustawienie aplikacji <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") spowoduje skonfigurowanie aplikacji do korzystania z usługi Google jako domyślnego schematu dla wywołań usługi `ChallengeAsync` . `DefaultChallengeScheme`zastąpień `DefaultScheme` . Zobacz <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> , aby uzyskać dodatkowe właściwości, które przesłonić, `DefaultScheme` gdy ustawione.

W `Configure` metodzie Wywołaj `UseAuthentication` metodę, aby wywołać oprogramowanie pośredniczące uwierzytelniania, które ustawia `HttpContext.User` Właściwość. Wywołaj `UseAuthentication` metodę przed wywołaniem `UseMvcWithDefaultRoute` lub `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Aby dowiedzieć się więcej o schematach uwierzytelniania, zobacz [pojęcia związane z uwierzytelnianiem](xref:security/authentication/index#authentication-concepts). Aby dowiedzieć się więcej o uwierzytelnianiu plików cookie, zobacz <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Zastosuj autoryzację

Przetestuj konfigurację uwierzytelniania aplikacji, stosując `AuthorizeAttribute` atrybut do kontrolera, akcji lub strony. Poniższy kod ogranicza dostęp do strony *prywatność* do użytkowników, którzy zostali uwierzytelnieni:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Wyloguj się

Aby wylogować bieżącego użytkownika i usunąć jego plik cookie, wywołaj [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Poniższy kod dodaje `Logout` procedurę obsługi strony do strony *indeks* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Zwróć uwagę, że wywołanie metody nie `SignOutAsync` określa schematu uwierzytelniania. Aplikacja `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` jest używana jako wraca.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
