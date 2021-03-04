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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Utrwalaj dodatkowe oświadczenia i tokeny od zewnętrznych dostawców w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Aplikacja ASP.NET Core może nawiązać dodatkowe oświadczenia i tokeny od zewnętrznych dostawców uwierzytelniania, takich jak Facebook, Google, Microsoft i Twitter. Każdy dostawca ujawnia różne informacje o użytkownikach na jego platformie, ale wzorzec do odbioru i przekształcania danych użytkownika w dodatkowe oświadczenia jest taki sam.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Zdecyduj, które zewnętrzne dostawcy uwierzytelniania mają być obsługiwane w aplikacji. Dla każdego dostawcy Zarejestruj aplikację i uzyskaj identyfikator klienta i klucz tajny klienta. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/index>. Przykładowa aplikacja używa [dostawcy uwierzytelniania Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Ustawianie identyfikatora klienta i klucza tajnego klienta

Dostawca uwierzytelniania OAuth ustanawia relację zaufania z aplikacją przy użyciu identyfikatora klienta i klucza tajnego klienta. Wartości identyfikator klienta i klucz tajny klienta są tworzone dla aplikacji przez zewnętrznego dostawcę uwierzytelniania, gdy aplikacja jest zarejestrowana w dostawcy. Każdy dostawca zewnętrzny, którego używa aplikacja, musi być skonfigurowany niezależnie od identyfikatora klienta dostawcy i klucza tajnego klienta. Aby uzyskać więcej informacji, zobacz tematy dotyczące zewnętrznego dostawcy uwierzytelniania, które dotyczą Twojego scenariusza:

* [Uwierzytelnianie przy użyciu usługi Facebook](xref:security/authentication/facebook-logins)
* [Uwierzytelnianie przy użyciu usługi Google](xref:security/authentication/google-logins)
* [Uwierzytelnianie firmy Microsoft](xref:security/authentication/microsoft-logins)
* [Uwierzytelnianie przy użyciu usługi Twitter](xref:security/authentication/twitter-logins)
* [Inni dostawcy uwierzytelniania](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Przykładowa aplikacja konfiguruje dostawcę uwierzytelniania Google przy użyciu identyfikatora klienta i klucza tajnego klienta dostarczonego przez firmę Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Ustanów zakres uwierzytelniania

Określ listę uprawnień do pobrania od dostawcy, określając <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . Zakresy uwierzytelniania dla typowych dostawców zewnętrznych są wyświetlane w poniższej tabeli.

| Dostawca  | Zakres                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `profile`, `email`, `openid`                                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

W przykładowej aplikacji,, `profile` `email` i `openid` zakresy, są automatycznie dodawane przez platformę, gdy <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> jest wywoływana na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Jeśli aplikacja wymaga dodatkowych zakresów, należy dodać je do opcji. W poniższym przykładzie `https://www.googleapis.com/auth/user.birthday.read` zostanie dodany zakres Google w celu pobrania urodzin użytkownika:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapuj klucze danych użytkownika i Utwórz oświadczenia

W opcjach dostawcy Określ <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> lub <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> dla każdego klucza/podklucza w danych użytkownika JSON dostawcy zewnętrznego, aby tożsamość aplikacji była odczytywana podczas logowania. Aby uzyskać więcej informacji na temat typów roszczeń, zobacz <xref:System.Security.Claims.ClaimTypes> .

Przykładowa aplikacja tworzy oświadczenia ustawień regionalnych ( `urn:google:locale` ) i obrazów ( `urn:google:picture` ) z `locale` `picture` kluczy i w danych użytkownika Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

W programie `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) jest zalogowany do aplikacji w usłudze <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . W procesie logowania <xref:Microsoft.AspNetCore.Identity.UserManager%601> można przechowywać `ApplicationUser` oświadczenia dla danych użytkownika dostępnych w <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

W przykładowej aplikacji `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) określa ustawienia regionalne ( `urn:google:locale` ) i obraz ( `urn:google:picture` ) dla zalogowanego elementu, w `ApplicationUser` tym oświadczenie dla <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Domyślnie oświadczenia użytkownika są przechowywane w ramach uwierzytelniania cookie . Jeśli uwierzytelnianie cookie jest zbyt duże, może to spowodować niepowodzenie aplikacji, ponieważ:

* Przeglądarka wykryje, że cookie nagłówek jest zbyt długi.
* Całkowity rozmiar żądania jest zbyt duży.

Jeśli do przetwarzania żądań użytkowników są wymagane duże ilości danych użytkownika:

* Ogranicz liczbę i rozmiar oświadczeń użytkowników do przetwarzania żądań tylko do wymaganej aplikacji.
* <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> Aby przechowywać tożsamość między żądaniami, Użyj niestandardowego oprogramowania pośredniczącego uwierzytelniania. Na serwerze są zachowywane duże ilości informacji o tożsamości, a do klienta jest wysyłany tylko mały klucz identyfikatora sesji.

## <a name="save-the-access-token"></a>Zapisz token dostępu

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Określa, czy tokeny dostępu i odświeżania mają być przechowywane w <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po pomyślnej autoryzacji. `SaveTokens` jest domyślnie ustawiona na wartość `false` , aby zmniejszyć rozmiar końcowego uwierzytelniania cookie .

Aplikacja Przykładowa ustawia wartość `SaveTokens` na `true` w <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Gdy jest `OnPostConfirmationAsync` wykonywane, należy przechowywać token dostępu ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z zewnętrznego dostawcy w `ApplicationUser` `AuthenticationProperties` .

Aplikacja Przykładowa zapisuje token dostępu w programie `OnPostConfirmationAsync` (Rejestracja nowego użytkownika) i `OnGetCallbackAsync` (wcześniej zarejestrowany użytkownik) w ramach *konta/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> Aby uzyskać informacje na temat przekazywania tokenów do Razor składników Blazor Server aplikacji, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .

## <a name="how-to-add-additional-custom-tokens"></a>Jak dodać dodatkowe tokeny niestandardowe

Aby zademonstrować sposób dodawania tokenu niestandardowego, który jest przechowywany w ramach programu `SaveTokens` , przykładowa aplikacja dodaje element <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> z bieżącą <xref:System.DateTime> [AuthenticationToken.Nameą](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Tworzenie i Dodawanie oświadczeń

Struktura zawiera typowe akcje i metody rozszerzające do tworzenia i dodawania oświadczeń do kolekcji. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> i <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .

Użytkownicy mogą definiować niestandardowe akcje, wyprowadzając z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> i implementując metodę abstrakcyjną <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .

Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="add-and-update-user-claims"></a>Dodawanie i aktualizowanie oświadczeń użytkowników

Oświadczenia są kopiowane od zewnętrznych dostawców do bazy danych użytkownika przy pierwszej rejestracji, a nie na zalogowaniu. Jeśli dodatkowe oświadczenia są włączone w aplikacji po zarejestrowaniu się użytkownika w celu korzystania z aplikacji, wywołaj [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) na użytkowniku, aby wymusić wygenerowanie nowego uwierzytelniania cookie .

W środowisku programistycznym pracującym z testowymi kontami użytkowników można po prostu usunąć i utworzyć ponownie konto użytkownika. W przypadku systemów produkcyjnych nowe oświadczenia dodane do aplikacji mogą być uzupełniane do kont użytkowników. Po utworzeniu [szkieletu `ExternalLogin` strony](xref:security/authentication/scaffold-identity) w aplikacji w `Areas/Pages/Identity/Account/Manage` programie Dodaj następujący kod do `ExternalLoginModel` `ExternalLogin.cshtml.cs` pliku.

Dodaj słownik dodanych oświadczeń. Użyj kluczy słownika do przechowywania typów roszczeń i użyj wartości do przechowywania wartości domyślnej. Dodaj następujący wiersz na początku klasy. W poniższym przykładzie przyjęto założenie, że jedno z nich zostanie dodane dla obrazu Google dla użytkownika z ogólnym obrazem headshot jako wartość domyślna:

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

Zastąp domyślny kod `OnGetCallbackAsync` metody poniższym kodem. Kod pętle za pomocą słownika oświadczeń. Oświadczenia są dodawane (uzupełniane) lub aktualizowane dla każdego użytkownika. Po dodaniu lub zaktualizowaniu oświadczeń Logowanie użytkownika jest odświeżane przy użyciu <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , zachowując istniejące właściwości uwierzytelniania ( `AuthenticationProperties` ).

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

Podobne podejście jest podejmowane podczas zmiany oświadczeń, gdy użytkownik jest zalogowany, ale krok wypełnienia nie jest wymagany. Aby zaktualizować oświadczenia użytkownika, wywołaj następujące polecenie na użytkowniku:

* [Usermanager. ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) na użytkowniku dla oświadczeń przechowywanych w bazie danych tożsamości.
* [SignInManager. RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) na użytkownika, aby wymusić wygenerowanie nowego uwierzytelniania cookie .

## <a name="removal-of-claim-actions-and-claims"></a>Usuwanie akcji oświadczeń i oświadczeń

[ClaimActionCollection. Remove (ciąg)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) usuwa wszystkie akcje roszczeń dla danego elementu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekcji. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) usuwa wniosek <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> pochodzący z tożsamości. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> jest używany przede wszystkim z [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) w celu usunięcia oświadczeń generowanych przez protokół.

## <a name="sample-app-output"></a>Przykładowe dane wyjściowe aplikacji

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

Aplikacja ASP.NET Core może nawiązać dodatkowe oświadczenia i tokeny od zewnętrznych dostawców uwierzytelniania, takich jak Facebook, Google, Microsoft i Twitter. Każdy dostawca ujawnia różne informacje o użytkownikach na jego platformie, ale wzorzec do odbioru i przekształcania danych użytkownika w dodatkowe oświadczenia jest taki sam.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Zdecyduj, które zewnętrzne dostawcy uwierzytelniania mają być obsługiwane w aplikacji. Dla każdego dostawcy Zarejestruj aplikację i uzyskaj identyfikator klienta i klucz tajny klienta. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/index>. Przykładowa aplikacja używa [dostawcy uwierzytelniania Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Ustawianie identyfikatora klienta i klucza tajnego klienta

Dostawca uwierzytelniania OAuth ustanawia relację zaufania z aplikacją przy użyciu identyfikatora klienta i klucza tajnego klienta. Wartości identyfikator klienta i klucz tajny klienta są tworzone dla aplikacji przez zewnętrznego dostawcę uwierzytelniania, gdy aplikacja jest zarejestrowana w dostawcy. Każdy dostawca zewnętrzny, którego używa aplikacja, musi być skonfigurowany niezależnie od identyfikatora klienta dostawcy i klucza tajnego klienta. Aby uzyskać więcej informacji, zobacz tematy dotyczące zewnętrznego dostawcy uwierzytelniania, które dotyczą Twojego scenariusza:

* [Uwierzytelnianie przy użyciu usługi Facebook](xref:security/authentication/facebook-logins)
* [Uwierzytelnianie przy użyciu usługi Google](xref:security/authentication/google-logins)
* [Uwierzytelnianie firmy Microsoft](xref:security/authentication/microsoft-logins)
* [Uwierzytelnianie przy użyciu usługi Twitter](xref:security/authentication/twitter-logins)
* [Inni dostawcy uwierzytelniania](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Przykładowa aplikacja konfiguruje dostawcę uwierzytelniania Google przy użyciu identyfikatora klienta i klucza tajnego klienta dostarczonego przez firmę Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Ustanów zakres uwierzytelniania

Określ listę uprawnień do pobrania od dostawcy, określając <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . Zakresy uwierzytelniania dla typowych dostawców zewnętrznych są wyświetlane w poniższej tabeli.

| Dostawca  | Zakres                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

W przykładowej aplikacji `userinfo.profile` zakres Google jest automatycznie dodawany przez platformę, gdy <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> jest wywoływana na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Jeśli aplikacja wymaga dodatkowych zakresów, należy dodać je do opcji. W poniższym przykładzie `https://www.googleapis.com/auth/user.birthday.read` zostanie dodany zakres Google w celu pobrania urodzin użytkownika:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapuj klucze danych użytkownika i Utwórz oświadczenia

W opcjach dostawcy Określ <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> lub <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> dla każdego klucza/podklucza w danych użytkownika JSON dostawcy zewnętrznego, aby tożsamość aplikacji była odczytywana podczas logowania. Aby uzyskać więcej informacji na temat typów roszczeń, zobacz <xref:System.Security.Claims.ClaimTypes> .

Przykładowa aplikacja tworzy oświadczenia ustawień regionalnych ( `urn:google:locale` ) i obrazów ( `urn:google:picture` ) z `locale` `picture` kluczy i w danych użytkownika Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

W programie `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) jest zalogowany do aplikacji w usłudze <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . W procesie logowania <xref:Microsoft.AspNetCore.Identity.UserManager%601> można przechowywać `ApplicationUser` oświadczenia dla danych użytkownika dostępnych w <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

W przykładowej aplikacji `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) określa ustawienia regionalne ( `urn:google:locale` ) i obraz ( `urn:google:picture` ) dla zalogowanego elementu, w `ApplicationUser` tym oświadczenie dla <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Domyślnie oświadczenia użytkownika są przechowywane w ramach uwierzytelniania cookie . Jeśli uwierzytelnianie cookie jest zbyt duże, może to spowodować niepowodzenie aplikacji, ponieważ:

* Przeglądarka wykryje, że cookie nagłówek jest zbyt długi.
* Całkowity rozmiar żądania jest zbyt duży.

Jeśli do przetwarzania żądań użytkowników są wymagane duże ilości danych użytkownika:

* Ogranicz liczbę i rozmiar oświadczeń użytkowników do przetwarzania żądań tylko do wymaganej aplikacji.
* <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> Aby przechowywać tożsamość między żądaniami, Użyj niestandardowego oprogramowania pośredniczącego uwierzytelniania. Na serwerze są zachowywane duże ilości informacji o tożsamości, a do klienta jest wysyłany tylko mały klucz identyfikatora sesji.

## <a name="save-the-access-token"></a>Zapisz token dostępu

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Określa, czy tokeny dostępu i odświeżania mają być przechowywane w <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po pomyślnej autoryzacji. `SaveTokens` jest domyślnie ustawiona na wartość `false` , aby zmniejszyć rozmiar końcowego uwierzytelniania cookie .

Aplikacja Przykładowa ustawia wartość `SaveTokens` na `true` w <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Gdy jest `OnPostConfirmationAsync` wykonywane, należy przechowywać token dostępu ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z zewnętrznego dostawcy w `ApplicationUser` `AuthenticationProperties` .

Aplikacja Przykładowa zapisuje token dostępu w programie `OnPostConfirmationAsync` (Rejestracja nowego użytkownika) i `OnGetCallbackAsync` (wcześniej zarejestrowany użytkownik) w ramach *konta/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Jak dodać dodatkowe tokeny niestandardowe

Aby zademonstrować sposób dodawania tokenu niestandardowego, który jest przechowywany w ramach programu `SaveTokens` , przykładowa aplikacja dodaje element <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> z bieżącą <xref:System.DateTime> [AuthenticationToken.Nameą](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Tworzenie i Dodawanie oświadczeń

Struktura zawiera typowe akcje i metody rozszerzające do tworzenia i dodawania oświadczeń do kolekcji. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> i <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .

Użytkownicy mogą definiować niestandardowe akcje, wyprowadzając z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> i implementując metodę abstrakcyjną <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .

Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Usuwanie akcji oświadczeń i oświadczeń

[ClaimActionCollection. Remove (ciąg)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) usuwa wszystkie akcje roszczeń dla danego elementu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekcji. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) usuwa wniosek <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> pochodzący z tożsamości. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> jest używany przede wszystkim z [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) w celu usunięcia oświadczeń generowanych przez protokół.

## <a name="sample-app-output"></a>Przykładowe dane wyjściowe aplikacji

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* [aplikacja AspNetCore inżynieryjna/SocialSample inżynierii](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): połączona Przykładowa aplikacja znajduje się w gałęzi inżyniera programu [dotnet/AspNetCore w witrynie GitHub](https://github.com/dotnet/AspNetCore) `master` . `master`Gałąź zawiera kod w ramach aktywnego programowania dla następnej wersji ASP.NET Core. Aby wyświetlić wersję przykładowej aplikacji dla wydanej wersji ASP.NET Core, Użyj listy rozwijanej **rozgałęzienie** , aby wybrać gałąź wydania (na przykład `release/{X.Y}` ).
