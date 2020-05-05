---
title: Konfigurowanie ASP.NET CoreIdentity
author: AdrienTorris
description: Poznaj ASP.NET Core Identity wartości domyślne i Dowiedz się, Identity jak skonfigurować właściwości do używania wartości niestandardowych.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: b88f2627eabc536f2d3b8e677020a67bfd1a40ba
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775651"
---
# <a name="configure-aspnet-core-identity"></a>Konfigurowanie tożsamości ASP.NET Core

ASP.NET Core Identity używa wartości domyślnych dla ustawień, takich jak zasady haseł, blokada i konfiguracja plików cookie. Te ustawienia można przesłonić w `Startup` klasie.

## <a name="identity-options"></a>Opcje tożsamości

Klasa [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) reprezentuje opcje, których można użyć do skonfigurowania systemu tożsamości. `IdentityOptions`musi być ustawiony **po** wywołaniu `AddIdentity` lub `AddDefaultIdentity`.

### <a name="claims-identity"></a>Tożsamość oświadczeń

[IdentityOptions. Identyfikator oświadczenia](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) określa [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) z właściwościami podanymi w poniższej tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Pobiera lub ustawia typ zgłoszenia używany dla żądania roli. | [Oświadczenia. rola](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Pobiera lub ustawia typ zgłoszenia używany dla żądania sygnatury zabezpieczeń. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Pobiera lub ustawia typ zgłoszenia używany dla żądania identyfikatora użytkownika. | [Oświadczenia. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Pobiera lub ustawia typ zgłoszenia używany jako nazwa użytkownika. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Blokad

Blokada została ustawiona w metodzie [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Poprzedni kod jest oparty na szablonie `Login` tożsamości. 

Opcje blokady są ustawione w `StartUp.ConfigureServices`:

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Poprzedni kod ustawia [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) z wartościami domyślnymi.

Pomyślne uwierzytelnienie resetuje liczbę prób dostępu zakończonych niepowodzeniem i resetuje zegar.

[IdentityOptions. Blokada](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) określa [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) z właściwościami przedstawionymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Określa, czy nowy użytkownik może być zablokowany. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Ilość czasu, przez który użytkownik jest blokowany, gdy następuje blokada. | 5 minut |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Liczba nieudanych prób dostępu do momentu zablokowania użytkownika, jeśli włączono blokadę. | 5 |

### <a name="password"></a>Hasło

Domyślnie tożsamość wymaga, aby hasła zawierały wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. Długość hasła musi wynosić co najmniej 6 znaków. [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) można ustawić w `Startup.ConfigureServices`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[IdentityOptions. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) określa [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) z właściwościami podanymi w tabeli.

::: moniker range=">= aspnetcore-2.0"

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Wymaga liczby z zakresu od 0-9 do hasła. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Minimalna długość hasła. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Wymaga małych liter w haśle. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Wymaga znaku niealfanumerycznego w haśle. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Dotyczy tylko ASP.NET Core 2,0 lub nowszych.<br><br> Wymaga podania liczby odrębnych znaków w haśle. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Wymaga wielkich liter w haśle. | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Wymaga liczby z zakresu od 0-9 do hasła. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Minimalna długość hasła. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Wymaga małych liter w haśle. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Wymaga znaku niealfanumerycznego w haśle. | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Wymaga wielkich liter w haśle. | `true` |

::: moniker-end

### <a name="sign-in"></a>Logowanie

Poniższy kod określa `SignIn` ustawienia (do wartości domyślnych):

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[IdentityOptions. Signer](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) określa [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) z właściwościami podanymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Do zalogowania się wymaga potwierdzonej wiadomości e-mail. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Wymaga potwierdzenia numeru telefonu w celu zalogowania się. | `false` |

### <a name="tokens"></a>Tokeny

[IdentityOptions. tokeny](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) określa [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) z właściwościami podanymi w tabeli.

|                                                        Właściwość                                                         |                                                                                      Opis                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       Pobiera lub ustawia `AuthenticatorTokenProvider` używany do weryfikacji logowania dwuskładnikowego przy użyciu wystawcy uwierzytelnienia.                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     Pobiera lub ustawia `ChangeEmailTokenProvider` używany do generowania tokenów używanych w wiadomościach e-mail z potwierdzeniem zmiany wiadomości e-mail.                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      Pobiera lub ustawia `ChangePhoneNumberTokenProvider` używany do generowania tokenów używanych podczas zmieniania numerów telefonów.                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             Pobiera lub ustawia dostawcę tokenu używanego do generowania tokenów używanych w wiadomościach e-mail z potwierdzeniem konta.                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | Pobiera lub ustawia [>IUserTwoFactorTokenProvider\<TUser](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) używany do generowania tokenów używanych w wiadomościach e-mail dotyczących resetowania haseł. |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                Służy do konstruowania [dostawcy tokenów użytkownika](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) z kluczem używanym jako nazwa dostawcy.                 |

### <a name="user"></a>Użytkownik

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[IdentityOptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) określa [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) z właściwościami podanymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Dozwolone znaki w nazwie użytkownika. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Wymaga, aby każdy użytkownik miał unikatową wiadomość e-mail. | `false` |

### <a name="cookie-settings"></a>Ustawienia plików cookie

Skonfiguruj plik cookie aplikacji w programie `Startup.ConfigureServices`. [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) musi być wywoływana **po** wywołaniu `AddIdentity` lub `AddDefaultIdentity`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

Aby uzyskać więcej informacji, zobacz [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Opcje skrótu hasła

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Pobiera i ustawia opcje tworzenia skrótów haseł.

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Tryb zgodności używany podczas mieszania nowych haseł. Wartość domyślna <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>to. Pierwszy bajt skrótu hasła zwanego *znacznikiem formatu*określa wersję algorytmu wyznaczania wartości skrótu używanego do mieszania hasła. Podczas weryfikowania hasła przy użyciu skrótu <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> Metoda wybiera poprawny algorytm na podstawie pierwszego bajtu. Klient może się uwierzytelnić niezależnie od tego, która wersja algorytmu została użyta do skrótu hasła. Ustawienie trybu zgodności ma wpływ na skróty *nowych haseł*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Liczba iteracji używanych podczas tworzenia skrótów haseł przy użyciu PBKDF2. Ta wartość jest używana tylko wtedy, <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> gdy jest ustawiona <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>na. Wartość musi być dodatnią liczbą całkowitą i wartością domyślną `10000`. |

W poniższym przykładzie <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> jest ustawiona na `12000` wartość w: `Startup.ConfigureServices`

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
