---
title: Konfigurowanie ASP.NET CoreIdentity
author: AdrienTorris
description: Poznaj ASP.NET Core Identity wartości domyślne i Dowiedz się, jak skonfigurować Identity właściwości do używania wartości niestandardowych.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 5c999b426742cf75b1997f5b40223e2dda112901
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160298"
---
# <a name="configure-aspnet-core-no-locidentity"></a>Konfigurowanie ASP.NET CoreIdentity

ASP.NET Core Identity używa domyślnych wartości ustawień, takich jak zasady haseł, blokada i konfiguracja plików cookie. Te ustawienia można przesłonić w `Startup` klasie.

## <a name="no-locidentity-options"></a>IdentityOpcje

Klasa [ Identity Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) reprezentuje opcje, których można użyć w celu skonfigurowania Identity systemu. `IdentityOptions`musi być ustawiony **po** wywołaniu `AddIdentity` lub `AddDefaultIdentity` .

### <a name="claims-no-locidentity"></a>OświadczeńIdentity

[ Identity Opcje. oświadczenia Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) określają [ Identity Opcje oświadczeń](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) z właściwościami podanymi w poniższej tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Pobiera lub ustawia typ zgłoszenia używany dla żądania roli. | [Oświadczenia. rola](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Pobiera lub ustawia typ zgłoszenia używany dla żądania sygnatury zabezpieczeń. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Pobiera lub ustawia typ zgłoszenia używany dla żądania identyfikatora użytkownika. | [Oświadczenia. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Pobiera lub ustawia typ zgłoszenia używany jako nazwa użytkownika. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Blokad

Blokada została ustawiona w metodzie [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Poprzedni kod jest oparty na `Login` Identity szablonie. 

Opcje blokady są ustawione w `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Poprzedni kod ustawia [ Identity Opcje](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) z wartościami domyślnymi.

Pomyślne uwierzytelnienie resetuje liczbę prób dostępu zakończonych niepowodzeniem i resetuje zegar.

[ Identity Options. Blokada](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) określa [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) z właściwościami pokazanymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Określa, czy nowy użytkownik może być zablokowany. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Ilość czasu, przez który użytkownik jest blokowany, gdy następuje blokada. | 5 min |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Liczba nieudanych prób dostępu do momentu zablokowania użytkownika, jeśli włączono blokadę. | 5 |

### <a name="password"></a>Hasło

Domyślnie program Identity wymaga, aby hasła zawierały wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. Długość hasła musi wynosić co najmniej 6 znaków.

Hasła są konfigurowane przy użyciu:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions>w programie `Startup.ConfigureServices` .
* [ `[StringLength]` atrybuty](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` właściwości, jeśli Identity są [szkieletami w aplikacji](xref:security/authentication/scaffold-identity). `InputModel``Password`właściwości znajdują się w następujących plikach:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) określa [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) z właściwościami podanymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Wymaga liczby z zakresu od 0-9 do hasła. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Minimalna długość hasła. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Wymaga małych liter w haśle. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Wymaga znaku niealfanumerycznego w haśle. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Dotyczy tylko ASP.NET Core 2,0 lub nowszych.<br><br> Wymaga podania liczby odrębnych znaków w haśle. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Wymaga wielkich liter w haśle. | `true` |

### <a name="sign-in"></a>Logowanie

Poniższy kod określa `SignIn` Ustawienia (do wartości domyślnych):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Opcje. Signer](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) określa [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) z właściwościami podanymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Do zalogowania się wymaga potwierdzonej wiadomości e-mail. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Wymaga potwierdzenia numeru telefonu w celu zalogowania się. | `false` |

### <a name="tokens"></a>Tokeny

[ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) określa [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) z właściwościami podanymi w tabeli.

| Właściwość | Opis |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Pobiera lub ustawia `AuthenticatorTokenProvider` używany do weryfikacji logowania dwuskładnikowego przy użyciu wystawcy uwierzytelnienia. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Pobiera lub ustawia `ChangeEmailTokenProvider` używany do generowania tokenów używanych w wiadomościach e-mail z potwierdzeniem zmiany wiadomości e-mail. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Pobiera lub ustawia `ChangePhoneNumberTokenProvider` używany do generowania tokenów używanych podczas zmieniania numerów telefonów. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Pobiera lub ustawia dostawcę tokenu używanego do generowania tokenów używanych w wiadomościach e-mail z potwierdzeniem konta. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Pobiera lub ustawia [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) używany do generowania tokenów używanych w wiadomościach e-mail dotyczących resetowania haseł. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Służy do konstruowania [dostawcy tokenów użytkownika](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) z kluczem używanym jako nazwa dostawcy. |

### <a name="user"></a>Użytkownik

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Opcje. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) określa [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) z właściwościami podanymi w tabeli.

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Dozwolone znaki w nazwie użytkownika. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Wymaga, aby każdy użytkownik miał unikatową wiadomość e-mail. | `false` |

### <a name="cookie-settings"></a>Ustawienia plików cookie

Skonfiguruj plik cookie aplikacji w programie `Startup.ConfigureServices` . [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) musi być wywoływana **po** wywołaniu `AddIdentity` lub `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Aby uzyskać więcej informacji, zobacz [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Opcje skrótu hasła

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Pobiera i ustawia opcje tworzenia skrótów haseł.

| Opcja | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Tryb zgodności używany podczas mieszania nowych haseł. Wartość domyślna to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Pierwszy bajt skrótu hasła zwanego *znacznikiem formatu*określa wersję algorytmu wyznaczania wartości skrótu używanego do mieszania hasła. Podczas weryfikowania hasła przy użyciu skrótu <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> Metoda wybiera poprawny algorytm na podstawie pierwszego bajtu. Klient może się uwierzytelnić niezależnie od tego, która wersja algorytmu została użyta do skrótu hasła. Ustawienie trybu zgodności ma wpływ na skróty *nowych haseł*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Liczba iteracji używanych podczas tworzenia skrótów haseł przy użyciu PBKDF2. Ta wartość jest używana tylko wtedy, gdy <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> jest ustawiona na <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Wartość musi być dodatnią liczbą całkowitą i wartością domyślną `10000` . |

W poniższym przykładzie <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> jest ustawiona na wartość `12000` w `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Globalnie Wymagaj uwierzytelnienia wszystkich użytkowników

[!INCLUDE[](~/includes/requireAuth.md)]