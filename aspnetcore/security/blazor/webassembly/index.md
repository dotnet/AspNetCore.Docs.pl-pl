---
title: Bezpieczne ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, Blazor jak zabezpieczyć aplikacje WebAssemlby jako aplikacje jednostronicowe (OSO).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661783"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="1b8ed-103">Bezpieczne ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1b8ed-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="1b8ed-104">Przez [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1b8ed-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="1b8ed-105">Aplikacje WebAssembly są zabezpieczone w taki sam sposób jak aplikacje jednostronicowe (OSO).</span><span class="sxs-lookup"><span data-stu-id="1b8ed-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="1b8ed-106">Istnieje kilka podejść do uwierzytelniania użytkowników do OSO, ale najbardziej powszechne i kompleksowe podejście jest użycie implementacji opartej na [protokole OAuth 2.0](https://oauth.net/), takich jak [Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="1b8ed-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="1b8ed-107">Biblioteka uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b8ed-107">Authentication library</span></span>

Blazor<span data-ttu-id="1b8ed-108">WebAssembly obsługuje uwierzytelnianie i autoryzowanie aplikacji `Microsoft.AspNetCore.Components.WebAssembly.Authentication` za pomocą OIDC za pośrednictwem biblioteki.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="1b8ed-109">Biblioteka zawiera zestaw podstawowych do bezproblemowego uwierzytelniania względem zaplecza ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="1b8ed-110">Biblioteka integruje ASP.NET Podstawową tożsamość z obsługą autoryzacji interfejsu API opartą na [serwerze tożsamości](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="1b8ed-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="1b8ed-111">Biblioteka może uwierzytelniać się względem dowolnego dostawcy tożsamości (IP) innej firmy, który obsługuje oidc, które są nazywane OpenID Providers (OP).</span><span class="sxs-lookup"><span data-stu-id="1b8ed-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="1b8ed-112">Obsługa uwierzytelniania Blazor w webassembly jest zbudowany na górze *biblioteki oidc-client.js,* który jest używany do obsługi szczegółów protokołu uwierzytelniania podstawowego.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="1b8ed-113">Istnieją inne opcje uwierzytelniania OSO, takie jak korzystanie z plików cookie SameSite.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="1b8ed-114">Jednak projekt inżynieryjny Blazor WebAssembly jest rozliczany na OAuth i OIDC jako najlepszą opcję uwierzytelniania w Blazor aplikacjach WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="1b8ed-115">[Uwierzytelnianie oparte](xref:security/anti-request-forgery#token-based-authentication) na tokenach oparte na [tokenach JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) zostało wybrane za pomocą [uwierzytelniania opartego na plikach cookie](xref:security/anti-request-forgery#cookie-based-authentication) ze względów funkcjonalnych i bezpieczeństwa:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="1b8ed-116">Za pomocą protokołu opartego na tokenie oferuje mniejszą powierzchnię ataku, jak tokeny nie są wysyłane we wszystkich żądaniach.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="1b8ed-117">Punkty końcowe serwera nie wymagają ochrony przed [fałszersko żądania między witrynami (CSRF),](xref:security/anti-request-forgery) ponieważ tokeny są wysyłane jawnie.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="1b8ed-118">Dzięki temu można Blazor hostować aplikacje WebAssembly obok aplikacji MVC lub Razor stron.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="1b8ed-119">Tokeny mają węższe uprawnienia niż pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="1b8ed-120">Na przykład tokeny nie mogą być używane do zarządzania kontem użytkownika lub zmiany hasła użytkownika, chyba że taka funkcja jest jawnie zaimplementowana.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="1b8ed-121">Tokeny mają krótki okres istnienia, domyślnie jedną godzinę, co ogranicza okno ataku.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="1b8ed-122">Tokeny można również odwołać w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="1b8ed-123">Samodzielne JWTs oferują gwarancje dla klienta i serwera dotyczące procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="1b8ed-124">Na przykład klient ma środki do wykrywania i sprawdzania poprawności, że tokeny, które otrzymuje są zgodne z prawem i zostały wyemitowane w ramach danego procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="1b8ed-125">Jeśli strona trzecia próbuje przełączyć token w środku procesu uwierzytelniania, klient może wykryć włączony token i uniknąć używania go.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="1b8ed-126">Tokeny z OAuth i OIDC nie opierają się na agenta użytkownika zachowuje się poprawnie, aby upewnić się, że aplikacja jest bezpieczna.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="1b8ed-127">Protokoły oparte na tokenach, takie jak OAuth i OIDC, umożliwiają uwierzytelnianie i autoryzowanie hostowanych i autonomicznych aplikacji z tym samym zestawem właściwości zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="1b8ed-128">Proces uwierzytelniania za pomocą OIDC</span><span class="sxs-lookup"><span data-stu-id="1b8ed-128">Authentication process with OIDC</span></span>

<span data-ttu-id="1b8ed-129">Biblioteka `Microsoft.AspNetCore.Components.WebAssembly.Authentication` oferuje kilka ujednolicenia do implementacji uwierzytelniania i autoryzacji przy użyciu OIDC.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="1b8ed-130">Ogólnie rzecz biorąc, uwierzytelnianie działa w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="1b8ed-131">Gdy użytkownik anonimowy wybierze przycisk logowania lub [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) zażąda strony z zastosowanym atrybutem, użytkownik zostanie`/authentication/login`przekierowany na stronę logowania aplikacji ( ).</span><span class="sxs-lookup"><span data-stu-id="1b8ed-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="1b8ed-132">Na stronie logowania biblioteka uwierzytelniania przygotowuje się do przekierowania do punktu końcowego autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="1b8ed-133">Punkt końcowy autoryzacji znajduje Blazor się poza aplikacją WebAssembly i może być obsługiwany w osobnym źródle pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="1b8ed-134">Punkt końcowy jest odpowiedzialny za określenie, czy użytkownik jest uwierzytelniony i do wystawiania jednego lub więcej tokenów w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="1b8ed-135">Biblioteka uwierzytelniania udostępnia wywołanie zwrotne logowania, aby otrzymać odpowiedź uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="1b8ed-136">Jeśli użytkownik nie jest uwierzytelniony, użytkownik jest przekierowywał do podstawowego systemu uwierzytelniania, który jest zwykle ASP.NET tożsamości podstawowej.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="1b8ed-137">Jeśli użytkownik został już uwierzytelniony, punkt końcowy autoryzacji generuje odpowiednie tokeny i przekierowuje`/authentication/login-callback`przeglądarkę z powrotem do punktu końcowego wywołania zwrotnego logowania ( ).</span><span class="sxs-lookup"><span data-stu-id="1b8ed-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="1b8ed-138">Gdy Blazor aplikacja WebAssembly ładuje punkt końcowy`/authentication/login-callback`wywołania zwrotnego logowania ( ), odpowiedź uwierzytelniania jest przetwarzana.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="1b8ed-139">Jeśli proces uwierzytelniania zakończy się pomyślnie, użytkownik jest uwierzytelniany i opcjonalnie odesłany do oryginalnego chronionego adresu URL, o który poprosił użytkownik.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="1b8ed-140">Jeśli proces uwierzytelniania nie powiedzie się z jakiegokolwiek powodu,`/authentication/login-failed`użytkownik jest wysyłany do strony logowania nie powiodło się ( ), a błąd jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="1b8ed-141">Obsługa prerenderingu za pomocą uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b8ed-141">Support prerendering with authentication</span></span>

<span data-ttu-id="1b8ed-142">Po zastosowaniu się do wskazówek Blazor w jednym z hostowanych tematów aplikacji WebAssembly, użyj następujących instrukcji, aby utworzyć aplikację, która:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="1b8ed-143">Ścieżki prerenders, dla których autoryzacja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="1b8ed-144">Nie jest prerender ścieżki, dla których wymagana jest autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="1b8ed-145">W `Program` klasie aplikacji klienta (*Program.cs*), współczynnik rejestracji wspólnej usługi w osobnej `ConfigureCommonServices`metodzie (na przykład):</span><span class="sxs-lookup"><span data-stu-id="1b8ed-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="1b8ed-146">W aplikacji `Startup.ConfigureServices`Serwer zarejestruj następujące usługi dodatkowe:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="1b8ed-147">W `Startup.Configure` metodzie aplikacji Serwer `endpoints.MapFallbackToFile("index.html")` zamień na `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="1b8ed-148">W aplikacji Serwer utwórz folder *Strony,* jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="1b8ed-149">Utwórz stronę *_Host.cshtml* w folderze *Strony aplikacji* Serwer.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="1b8ed-150">Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *Pages/_Host.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="1b8ed-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="1b8ed-151">Zaktualizuj zawartość pliku:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-151">Update the file's contents:</span></span>

* <span data-ttu-id="1b8ed-152">Dodaj `@page "_Host"` do górnej części pliku.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="1b8ed-153">Zastąp `<app>Loading...</app>` znacznik następującym:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="1b8ed-154">Opcje dla hostowanych aplikacji i dostawców logowania innych firm</span><span class="sxs-lookup"><span data-stu-id="1b8ed-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="1b8ed-155">Podczas uwierzytelniania i autoryzowania Blazor hosta aplikacji WebAssembly z dostawcą innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="1b8ed-156">Który z nich wybierzesz zależy od scenariusza.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="1b8ed-157">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="1b8ed-158">Uwierzytelnij użytkowników, aby wywoływać tylko chronione interfejsy API innych firm</span><span class="sxs-lookup"><span data-stu-id="1b8ed-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="1b8ed-159">Uwierzytelnij użytkownika przy użyciu przepływu OAuth po stronie klienta względem zewnętrznego dostawcy interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="1b8ed-160">W tym scenariuszu:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-160">In this scenario:</span></span>

* <span data-ttu-id="1b8ed-161">Serwer obsługujący aplikację nie odgrywa roli.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="1b8ed-162">Nie można chronić interfejsów API na serwerze.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="1b8ed-163">Aplikacja może wywoływać tylko chronione interfejsy API innych firm.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="1b8ed-164">Uwierzytelnij użytkowników za pomocą zewnętrznego dostawcy i wywołaj chronione interfejsy API na serwerze hosta i u innej firmy</span><span class="sxs-lookup"><span data-stu-id="1b8ed-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="1b8ed-165">Skonfiguruj tożsamość za pomocą zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="1b8ed-166">Uzyskaj tokeny wymagane do dostępu do interfejsu API innych firm i przechowuj je.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="1b8ed-167">Gdy użytkownik się loguje, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="1b8ed-168">W tym momencie istnieje kilka metod dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="1b8ed-169">Pobieranie tokenu dostępu innej firmy za pomocą tokenu dostępu do serwera</span><span class="sxs-lookup"><span data-stu-id="1b8ed-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="1b8ed-170">Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="1b8ed-171">Stamtąd użyj tokenu dostępu innej firmy, aby wywołać zasoby interfejsu API innych firm bezpośrednio z tożsamości na kliencie.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="1b8ed-172">Nie zalecamy takiego podejścia.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-172">We don't recommend this approach.</span></span> <span data-ttu-id="1b8ed-173">Takie podejście wymaga traktowania tokenu dostępu innej firmy tak, jakby zostały wygenerowane dla klienta publicznego.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="1b8ed-174">W terminach OAuth publiczna aplikacja nie ma klucza tajnego klienta, ponieważ nie można ufać, aby bezpiecznie przechowywać wpisy tajne, a token dostępu jest produkowany dla poufnego klienta.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="1b8ed-175">Klient poufny jest klientem, który ma klucz tajny klienta i zakłada się, że może bezpiecznie przechowywać wpisy tajne.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="1b8ed-176">Token dostępu innej firmy może otrzymać dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że innej firmy emitowane tokenu dla bardziej zaufanego klienta.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="1b8ed-177">Podobnie tokeny odświeżania nie powinny być wystawiane klientowi, który nie jest zaufany, ponieważ w ten sposób daje klientowi nieograniczony dostęp, chyba że zostaną wprowadzone inne ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="1b8ed-178">Wykonuj wywołania interfejsu API od klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="1b8ed-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="1b8ed-179">Nawład wywołanie interfejsu API od klienta do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="1b8ed-180">Z serwera pobierz token dostępu dla zasobu interfejsu API innej firmy i wydaj cokolwiek wywołania jest konieczne.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="1b8ed-181">Takie podejście wymaga dodatkowego przeskoku sieciowego za pośrednictwem serwera, aby wywołać interfejs API innej firmy, ostatecznie powoduje to bezpieczniejsze środowisko:</span><span class="sxs-lookup"><span data-stu-id="1b8ed-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="1b8ed-182">Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="1b8ed-183">Aplikacja nie może wyciekać tokenów dostępu z serwera, które mogą zawierać bardziej poufne uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="1b8ed-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
