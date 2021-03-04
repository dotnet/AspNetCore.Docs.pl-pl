---
title: Utrwalaj dodatkowe oświadczenia i tokeny od zewnętrznych dostawców w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ustanowić dodatkowe oświadczenia i tokeny od zewnętrznych dostawców.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
no-loc:
- appsettings.json
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 9c04ca466566e956b5e6dfec8131096c3995bc14
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110147"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="bf252-103">Utrwalaj dodatkowe oświadczenia i tokeny od zewnętrznych dostawców w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf252-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bf252-104">Aplikacja ASP.NET Core może nawiązać dodatkowe oświadczenia i tokeny od zewnętrznych dostawców uwierzytelniania, takich jak Facebook, Google, Microsoft i Twitter.</span><span class="sxs-lookup"><span data-stu-id="bf252-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="bf252-105">Każdy dostawca ujawnia różne informacje o użytkownikach na jego platformie, ale wzorzec do odbioru i przekształcania danych użytkownika w dodatkowe oświadczenia jest taki sam.</span><span class="sxs-lookup"><span data-stu-id="bf252-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="bf252-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bf252-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bf252-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="bf252-107">Prerequisites</span></span>

<span data-ttu-id="bf252-108">Zdecyduj, które zewnętrzne dostawcy uwierzytelniania mają być obsługiwane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bf252-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="bf252-109">Dla każdego dostawcy Zarejestruj aplikację i uzyskaj identyfikator klienta i klucz tajny klienta.</span><span class="sxs-lookup"><span data-stu-id="bf252-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="bf252-110">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="bf252-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="bf252-111">Przykładowa aplikacja używa [dostawcy uwierzytelniania Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="bf252-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="bf252-112">Ustawianie identyfikatora klienta i klucza tajnego klienta</span><span class="sxs-lookup"><span data-stu-id="bf252-112">Set the client ID and client secret</span></span>

<span data-ttu-id="bf252-113">Dostawca uwierzytelniania OAuth ustanawia relację zaufania z aplikacją przy użyciu identyfikatora klienta i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="bf252-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="bf252-114">Wartości identyfikator klienta i klucz tajny klienta są tworzone dla aplikacji przez zewnętrznego dostawcę uwierzytelniania, gdy aplikacja jest zarejestrowana w dostawcy.</span><span class="sxs-lookup"><span data-stu-id="bf252-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="bf252-115">Każdy dostawca zewnętrzny, którego używa aplikacja, musi być skonfigurowany niezależnie od identyfikatora klienta dostawcy i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="bf252-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="bf252-116">Aby uzyskać więcej informacji, zobacz tematy dotyczące zewnętrznego dostawcy uwierzytelniania, które dotyczą Twojego scenariusza:</span><span class="sxs-lookup"><span data-stu-id="bf252-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="bf252-117">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="bf252-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="bf252-118">Uwierzytelnianie przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="bf252-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="bf252-119">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="bf252-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="bf252-120">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="bf252-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="bf252-121">Inni dostawcy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bf252-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="bf252-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="bf252-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="bf252-123">Przykładowa aplikacja konfiguruje dostawcę uwierzytelniania Google przy użyciu identyfikatora klienta i klucza tajnego klienta dostarczonego przez firmę Google:</span><span class="sxs-lookup"><span data-stu-id="bf252-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="bf252-124">Ustanów zakres uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bf252-124">Establish the authentication scope</span></span>

<span data-ttu-id="bf252-125">Określ listę uprawnień do pobrania od dostawcy, określając <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="bf252-126">Zakresy uwierzytelniania dla typowych dostawców zewnętrznych są wyświetlane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="bf252-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="bf252-127">Dostawca</span><span class="sxs-lookup"><span data-stu-id="bf252-127">Provider</span></span>  | <span data-ttu-id="bf252-128">Zakres</span><span class="sxs-lookup"><span data-stu-id="bf252-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="bf252-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="bf252-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="bf252-130">Google</span><span class="sxs-lookup"><span data-stu-id="bf252-130">Google</span></span>    | <span data-ttu-id="bf252-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="bf252-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="bf252-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="bf252-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="bf252-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="bf252-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="bf252-134">W przykładowej aplikacji,, `profile` `email` i `openid` zakresy, są automatycznie dodawane przez platformę, gdy <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> jest wywoływana na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="bf252-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="bf252-135">Jeśli aplikacja wymaga dodatkowych zakresów, należy dodać je do opcji.</span><span class="sxs-lookup"><span data-stu-id="bf252-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="bf252-136">W poniższym przykładzie `https://www.googleapis.com/auth/user.birthday.read` zostanie dodany zakres Google w celu pobrania urodzin użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bf252-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="bf252-137">Mapuj klucze danych użytkownika i Utwórz oświadczenia</span><span class="sxs-lookup"><span data-stu-id="bf252-137">Map user data keys and create claims</span></span>

<span data-ttu-id="bf252-138">W opcjach dostawcy Określ <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> lub <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> dla każdego klucza/podklucza w danych użytkownika JSON dostawcy zewnętrznego, aby tożsamość aplikacji była odczytywana podczas logowania.</span><span class="sxs-lookup"><span data-stu-id="bf252-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="bf252-139">Aby uzyskać więcej informacji na temat typów roszczeń, zobacz <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="bf252-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="bf252-140">Przykładowa aplikacja tworzy oświadczenia ustawień regionalnych ( `urn:google:locale` ) i obrazów ( `urn:google:picture` ) z `locale` `picture` kluczy i w danych użytkownika Google:</span><span class="sxs-lookup"><span data-stu-id="bf252-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="bf252-141">W programie `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) jest zalogowany do aplikacji w usłudze <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="bf252-142">W procesie logowania <xref:Microsoft.AspNetCore.Identity.UserManager%601> można przechowywać `ApplicationUser` oświadczenia dla danych użytkownika dostępnych w <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="bf252-143">W przykładowej aplikacji `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) określa ustawienia regionalne ( `urn:google:locale` ) i obraz ( `urn:google:picture` ) dla zalogowanego elementu, w `ApplicationUser` tym oświadczenie dla <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="bf252-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="bf252-144">Domyślnie oświadczenia użytkownika są przechowywane w ramach uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="bf252-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="bf252-145">Jeśli uwierzytelnianie cookie jest zbyt duże, może to spowodować niepowodzenie aplikacji, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="bf252-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="bf252-146">Przeglądarka wykryje, że cookie nagłówek jest zbyt długi.</span><span class="sxs-lookup"><span data-stu-id="bf252-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="bf252-147">Całkowity rozmiar żądania jest zbyt duży.</span><span class="sxs-lookup"><span data-stu-id="bf252-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="bf252-148">Jeśli do przetwarzania żądań użytkowników są wymagane duże ilości danych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bf252-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="bf252-149">Ogranicz liczbę i rozmiar oświadczeń użytkowników do przetwarzania żądań tylko do wymaganej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bf252-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="bf252-150"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> Aby przechowywać tożsamość między żądaniami, Użyj niestandardowego oprogramowania pośredniczącego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="bf252-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="bf252-151">Na serwerze są zachowywane duże ilości informacji o tożsamości, a do klienta jest wysyłany tylko mały klucz identyfikatora sesji.</span><span class="sxs-lookup"><span data-stu-id="bf252-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="bf252-152">Zapisz token dostępu</span><span class="sxs-lookup"><span data-stu-id="bf252-152">Save the access token</span></span>

<span data-ttu-id="bf252-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Określa, czy tokeny dostępu i odświeżania mają być przechowywane w <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po pomyślnej autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="bf252-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="bf252-154">`SaveTokens` jest domyślnie ustawiona na wartość `false` , aby zmniejszyć rozmiar końcowego uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="bf252-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="bf252-155">Aplikacja Przykładowa ustawia wartość `SaveTokens` na `true` w <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="bf252-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="bf252-156">Gdy jest `OnPostConfirmationAsync` wykonywane, należy przechowywać token dostępu ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z zewnętrznego dostawcy w `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="bf252-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="bf252-157">Aplikacja Przykładowa zapisuje token dostępu w programie `OnPostConfirmationAsync` (Rejestracja nowego użytkownika) i `OnGetCallbackAsync` (wcześniej zarejestrowany użytkownik) w ramach *konta/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="bf252-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="bf252-158">Aby uzyskać informacje na temat przekazywania tokenów do Razor składników Blazor Server aplikacji, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .</span><span class="sxs-lookup"><span data-stu-id="bf252-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="bf252-159">Jak dodać dodatkowe tokeny niestandardowe</span><span class="sxs-lookup"><span data-stu-id="bf252-159">How to add additional custom tokens</span></span>

<span data-ttu-id="bf252-160">Aby zademonstrować sposób dodawania tokenu niestandardowego, który jest przechowywany w ramach programu `SaveTokens` , przykładowa aplikacja dodaje element <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> z bieżącą <xref:System.DateTime> [AuthenticationToken.Nameą](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="bf252-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="bf252-161">Tworzenie i Dodawanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="bf252-161">Creating and adding claims</span></span>

<span data-ttu-id="bf252-162">Struktura zawiera typowe akcje i metody rozszerzające do tworzenia i dodawania oświadczeń do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="bf252-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="bf252-163">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> i <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="bf252-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="bf252-164">Użytkownicy mogą definiować niestandardowe akcje, wyprowadzając z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> i implementując metodę abstrakcyjną <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="bf252-165">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="bf252-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="bf252-166">Dodawanie i aktualizowanie oświadczeń użytkowników</span><span class="sxs-lookup"><span data-stu-id="bf252-166">Add and update user claims</span></span>

<span data-ttu-id="bf252-167">Oświadczenia są kopiowane od zewnętrznych dostawców do bazy danych użytkownika przy pierwszej rejestracji, a nie na zalogowaniu.</span><span class="sxs-lookup"><span data-stu-id="bf252-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="bf252-168">Jeśli dodatkowe oświadczenia są włączone w aplikacji po zarejestrowaniu się użytkownika w celu korzystania z aplikacji, wywołaj [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) na użytkowniku, aby wymusić wygenerowanie nowego uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="bf252-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="bf252-169">W środowisku programistycznym pracującym z testowymi kontami użytkowników można po prostu usunąć i utworzyć ponownie konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bf252-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="bf252-170">W przypadku systemów produkcyjnych nowe oświadczenia dodane do aplikacji mogą być uzupełniane do kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="bf252-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="bf252-171">Po utworzeniu [szkieletu `ExternalLogin` strony](xref:security/authentication/scaffold-identity) w aplikacji w `Areas/Pages/Identity/Account/Manage` programie Dodaj następujący kod do `ExternalLoginModel` `ExternalLogin.cshtml.cs` pliku.</span><span class="sxs-lookup"><span data-stu-id="bf252-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="bf252-172">Dodaj słownik dodanych oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="bf252-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="bf252-173">Użyj kluczy słownika do przechowywania typów roszczeń i użyj wartości do przechowywania wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="bf252-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="bf252-174">Dodaj następujący wiersz na początku klasy.</span><span class="sxs-lookup"><span data-stu-id="bf252-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="bf252-175">W poniższym przykładzie przyjęto założenie, że jedno z nich zostanie dodane dla obrazu Google dla użytkownika z ogólnym obrazem headshot jako wartość domyślna:</span><span class="sxs-lookup"><span data-stu-id="bf252-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="bf252-176">Zastąp domyślny kod `OnGetCallbackAsync` metody poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="bf252-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="bf252-177">Kod pętle za pomocą słownika oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="bf252-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="bf252-178">Oświadczenia są dodawane (uzupełniane) lub aktualizowane dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bf252-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="bf252-179">Po dodaniu lub zaktualizowaniu oświadczeń Logowanie użytkownika jest odświeżane przy użyciu <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , zachowując istniejące właściwości uwierzytelniania ( `AuthenticationProperties` ).</span><span class="sxs-lookup"><span data-stu-id="bf252-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

<span data-ttu-id="bf252-180">Podobne podejście jest podejmowane podczas zmiany oświadczeń, gdy użytkownik jest zalogowany, ale krok wypełnienia nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="bf252-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="bf252-181">Aby zaktualizować oświadczenia użytkownika, wywołaj następujące polecenie na użytkowniku:</span><span class="sxs-lookup"><span data-stu-id="bf252-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="bf252-182">[Usermanager. ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) na użytkowniku dla oświadczeń przechowywanych w bazie danych tożsamości.</span><span class="sxs-lookup"><span data-stu-id="bf252-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="bf252-183">[SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) na użytkownika, aby wymusić wygenerowanie nowego uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="bf252-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="bf252-184">Usuwanie akcji oświadczeń i oświadczeń</span><span class="sxs-lookup"><span data-stu-id="bf252-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="bf252-185">[ClaimActionCollection. Remove (ciąg)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) usuwa wszystkie akcje roszczeń dla danego elementu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="bf252-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="bf252-186">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) usuwa wniosek <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> pochodzący z tożsamości.</span><span class="sxs-lookup"><span data-stu-id="bf252-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="bf252-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> jest używany przede wszystkim z [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) w celu usunięcia oświadczeń generowanych przez protokół.</span><span class="sxs-lookup"><span data-stu-id="bf252-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="bf252-188">Przykładowe dane wyjściowe aplikacji</span><span class="sxs-lookup"><span data-stu-id="bf252-188">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf252-189">Aplikacja ASP.NET Core może nawiązać dodatkowe oświadczenia i tokeny od zewnętrznych dostawców uwierzytelniania, takich jak Facebook, Google, Microsoft i Twitter.</span><span class="sxs-lookup"><span data-stu-id="bf252-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="bf252-190">Każdy dostawca ujawnia różne informacje o użytkownikach na jego platformie, ale wzorzec do odbioru i przekształcania danych użytkownika w dodatkowe oświadczenia jest taki sam.</span><span class="sxs-lookup"><span data-stu-id="bf252-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="bf252-191">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bf252-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bf252-192">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="bf252-192">Prerequisites</span></span>

<span data-ttu-id="bf252-193">Zdecyduj, które zewnętrzne dostawcy uwierzytelniania mają być obsługiwane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bf252-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="bf252-194">Dla każdego dostawcy Zarejestruj aplikację i uzyskaj identyfikator klienta i klucz tajny klienta.</span><span class="sxs-lookup"><span data-stu-id="bf252-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="bf252-195">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="bf252-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="bf252-196">Przykładowa aplikacja używa [dostawcy uwierzytelniania Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="bf252-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="bf252-197">Ustawianie identyfikatora klienta i klucza tajnego klienta</span><span class="sxs-lookup"><span data-stu-id="bf252-197">Set the client ID and client secret</span></span>

<span data-ttu-id="bf252-198">Dostawca uwierzytelniania OAuth ustanawia relację zaufania z aplikacją przy użyciu identyfikatora klienta i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="bf252-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="bf252-199">Wartości identyfikator klienta i klucz tajny klienta są tworzone dla aplikacji przez zewnętrznego dostawcę uwierzytelniania, gdy aplikacja jest zarejestrowana w dostawcy.</span><span class="sxs-lookup"><span data-stu-id="bf252-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="bf252-200">Każdy dostawca zewnętrzny, którego używa aplikacja, musi być skonfigurowany niezależnie od identyfikatora klienta dostawcy i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="bf252-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="bf252-201">Aby uzyskać więcej informacji, zobacz tematy dotyczące zewnętrznego dostawcy uwierzytelniania, które dotyczą Twojego scenariusza:</span><span class="sxs-lookup"><span data-stu-id="bf252-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="bf252-202">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="bf252-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="bf252-203">Uwierzytelnianie przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="bf252-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="bf252-204">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="bf252-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="bf252-205">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="bf252-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="bf252-206">Inni dostawcy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bf252-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="bf252-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="bf252-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="bf252-208">Przykładowa aplikacja konfiguruje dostawcę uwierzytelniania Google przy użyciu identyfikatora klienta i klucza tajnego klienta dostarczonego przez firmę Google:</span><span class="sxs-lookup"><span data-stu-id="bf252-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="bf252-209">Ustanów zakres uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bf252-209">Establish the authentication scope</span></span>

<span data-ttu-id="bf252-210">Określ listę uprawnień do pobrania od dostawcy, określając <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="bf252-211">Zakresy uwierzytelniania dla typowych dostawców zewnętrznych są wyświetlane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="bf252-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="bf252-212">Dostawca</span><span class="sxs-lookup"><span data-stu-id="bf252-212">Provider</span></span>  | <span data-ttu-id="bf252-213">Zakres</span><span class="sxs-lookup"><span data-stu-id="bf252-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="bf252-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="bf252-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="bf252-215">Google</span><span class="sxs-lookup"><span data-stu-id="bf252-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="bf252-216">Microsoft</span><span class="sxs-lookup"><span data-stu-id="bf252-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="bf252-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="bf252-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="bf252-218">W przykładowej aplikacji `userinfo.profile` zakres Google jest automatycznie dodawany przez platformę, gdy <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> jest wywoływana na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="bf252-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="bf252-219">Jeśli aplikacja wymaga dodatkowych zakresów, należy dodać je do opcji.</span><span class="sxs-lookup"><span data-stu-id="bf252-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="bf252-220">W poniższym przykładzie `https://www.googleapis.com/auth/user.birthday.read` zostanie dodany zakres Google w celu pobrania urodzin użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bf252-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="bf252-221">Mapuj klucze danych użytkownika i Utwórz oświadczenia</span><span class="sxs-lookup"><span data-stu-id="bf252-221">Map user data keys and create claims</span></span>

<span data-ttu-id="bf252-222">W opcjach dostawcy Określ <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> lub <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> dla każdego klucza/podklucza w danych użytkownika JSON dostawcy zewnętrznego, aby tożsamość aplikacji była odczytywana podczas logowania.</span><span class="sxs-lookup"><span data-stu-id="bf252-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="bf252-223">Aby uzyskać więcej informacji na temat typów roszczeń, zobacz <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="bf252-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="bf252-224">Przykładowa aplikacja tworzy oświadczenia ustawień regionalnych ( `urn:google:locale` ) i obrazów ( `urn:google:picture` ) z `locale` `picture` kluczy i w danych użytkownika Google:</span><span class="sxs-lookup"><span data-stu-id="bf252-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="bf252-225">W programie `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) jest zalogowany do aplikacji w usłudze <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="bf252-226">W procesie logowania <xref:Microsoft.AspNetCore.Identity.UserManager%601> można przechowywać `ApplicationUser` oświadczenia dla danych użytkownika dostępnych w <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="bf252-227">W przykładowej aplikacji `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) określa ustawienia regionalne ( `urn:google:locale` ) i obraz ( `urn:google:picture` ) dla zalogowanego elementu, w `ApplicationUser` tym oświadczenie dla <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="bf252-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="bf252-228">Domyślnie oświadczenia użytkownika są przechowywane w ramach uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="bf252-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="bf252-229">Jeśli uwierzytelnianie cookie jest zbyt duże, może to spowodować niepowodzenie aplikacji, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="bf252-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="bf252-230">Przeglądarka wykryje, że cookie nagłówek jest zbyt długi.</span><span class="sxs-lookup"><span data-stu-id="bf252-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="bf252-231">Całkowity rozmiar żądania jest zbyt duży.</span><span class="sxs-lookup"><span data-stu-id="bf252-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="bf252-232">Jeśli do przetwarzania żądań użytkowników są wymagane duże ilości danych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="bf252-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="bf252-233">Ogranicz liczbę i rozmiar oświadczeń użytkowników do przetwarzania żądań tylko do wymaganej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bf252-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="bf252-234"><xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> Aby przechowywać tożsamość między żądaniami, Użyj niestandardowego oprogramowania pośredniczącego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="bf252-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="bf252-235">Na serwerze są zachowywane duże ilości informacji o tożsamości, a do klienta jest wysyłany tylko mały klucz identyfikatora sesji.</span><span class="sxs-lookup"><span data-stu-id="bf252-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="bf252-236">Zapisz token dostępu</span><span class="sxs-lookup"><span data-stu-id="bf252-236">Save the access token</span></span>

<span data-ttu-id="bf252-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Określa, czy tokeny dostępu i odświeżania mają być przechowywane w <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po pomyślnej autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="bf252-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="bf252-238">`SaveTokens` jest domyślnie ustawiona na wartość `false` , aby zmniejszyć rozmiar końcowego uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="bf252-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="bf252-239">Aplikacja Przykładowa ustawia wartość `SaveTokens` na `true` w <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="bf252-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="bf252-240">Gdy jest `OnPostConfirmationAsync` wykonywane, należy przechowywać token dostępu ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z zewnętrznego dostawcy w `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="bf252-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="bf252-241">Aplikacja Przykładowa zapisuje token dostępu w programie `OnPostConfirmationAsync` (Rejestracja nowego użytkownika) i `OnGetCallbackAsync` (wcześniej zarejestrowany użytkownik) w ramach *konta/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="bf252-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="bf252-242">Jak dodać dodatkowe tokeny niestandardowe</span><span class="sxs-lookup"><span data-stu-id="bf252-242">How to add additional custom tokens</span></span>

<span data-ttu-id="bf252-243">Aby zademonstrować sposób dodawania tokenu niestandardowego, który jest przechowywany w ramach programu `SaveTokens` , przykładowa aplikacja dodaje element <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> z bieżącą <xref:System.DateTime> [AuthenticationToken.Nameą](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="bf252-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="bf252-244">Tworzenie i Dodawanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="bf252-244">Creating and adding claims</span></span>

<span data-ttu-id="bf252-245">Struktura zawiera typowe akcje i metody rozszerzające do tworzenia i dodawania oświadczeń do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="bf252-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="bf252-246">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> i <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="bf252-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="bf252-247">Użytkownicy mogą definiować niestandardowe akcje, wyprowadzając z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> i implementując metodę abstrakcyjną <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="bf252-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="bf252-248">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="bf252-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="bf252-249">Usuwanie akcji oświadczeń i oświadczeń</span><span class="sxs-lookup"><span data-stu-id="bf252-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="bf252-250">[ClaimActionCollection. Remove (ciąg)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) usuwa wszystkie akcje roszczeń dla danego elementu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="bf252-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="bf252-251">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) usuwa wniosek <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> pochodzący z tożsamości.</span><span class="sxs-lookup"><span data-stu-id="bf252-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="bf252-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> jest używany przede wszystkim z [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) w celu usunięcia oświadczeń generowanych przez protokół.</span><span class="sxs-lookup"><span data-stu-id="bf252-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="bf252-253">Przykładowe dane wyjściowe aplikacji</span><span class="sxs-lookup"><span data-stu-id="bf252-253">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bf252-254">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bf252-254">Additional resources</span></span>

* <span data-ttu-id="bf252-255">[aplikacja AspNetCore inżynieryjna/SocialSample inżynierii](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): połączona Przykładowa aplikacja znajduje się w gałęzi inżyniera programu [dotnet/AspNetCore w witrynie GitHub](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="bf252-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="bf252-256">`master`Gałąź zawiera kod w ramach aktywnego programowania dla następnej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf252-256">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="bf252-257">Aby wyświetlić wersję przykładowej aplikacji dla wydanej wersji ASP.NET Core, Użyj listy rozwijanej **rozgałęzienie** , aby wybrać gałąź wydania (na przykład `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="bf252-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
