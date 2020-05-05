---
title: Autoryzuj z określonym schematem w ASP.NET Core
author: rick-anderson
description: W tym artykule wyjaśniono, jak ograniczyć tożsamość do określonego schematu podczas pracy z wieloma metodami uwierzytelniania.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 69b6412f249355573faa785743b124a67ecb8b9e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777517"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autoryzuj z określonym schematem w ASP.NET Core

W niektórych scenariuszach, takich jak aplikacje jednostronicowe (aplikacji jednostronicowych), często używane są wiele metod uwierzytelniania. Na przykład aplikacja może używać uwierzytelniania opartego na plikach cookie do logowania się i uwierzytelniania JWT dla żądań języka JavaScript. W niektórych przypadkach aplikacja może mieć wiele wystąpień programu obsługi uwierzytelniania. Na przykład dwa programy obsługi plików cookie, w których jeden zawiera tożsamość podstawową, a jedna jest tworzona podczas wyzwalania uwierzytelniania wieloskładnikowego (MFA). Uwierzytelnianie wieloskładnikowe może być wyzwalane, ponieważ użytkownik zażądał operacji wymagającej dodatkowych zabezpieczeń. Aby uzyskać więcej informacji na temat wymuszania usługi MFA, gdy użytkownik zażąda zasobu wymagającego uwierzytelniania wieloskładnikowego, zobacz [sekcję dotyczącą ochrony za pomocą](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)usługi GitHub i usługi MFA

Schemat uwierzytelniania ma nazwę, gdy usługa uwierzytelniania jest konfigurowana podczas uwierzytelniania. Przykład:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

W poprzednim kodzie dodano dwa programy obsługi uwierzytelniania: jeden dla plików cookie i jeden dla okaziciela.

>[!NOTE]
>Określenie schematu domyślnego powoduje, że `HttpContext.User` właściwość jest ustawiana na tę tożsamość. Jeśli takie zachowanie nie jest wymagane, należy je wyłączyć, wywołując formularz bez parametrów `AddAuthentication`.

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Wybieranie schematu z atrybutem Autoryzuj

W punkcie autoryzacji Aplikacja wskazuje program obsługi, który ma być używany. Wybierz program obsługi, za pomocą którego aplikacja będzie autoryzować, przekazując rozdzieloną przecinkami listę schematów uwierzytelniania do `[Authorize]`. Ten `[Authorize]` atrybut określa schemat lub schematy uwierzytelniania, które mają być używane niezależnie od tego, czy skonfigurowano wartość domyślną. Przykład:

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

W poprzednim przykładzie uruchomiono zarówno programy obsługi plików cookie, jak i okaziciela oraz możliwość tworzenia i dołączania tożsamości bieżącego użytkownika. Określając tylko jeden schemat, zostanie uruchomiony odpowiedni program obsługi.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

W poprzednim kodzie jest tylko procedura obsługi ze schematem "Bearer". Wszystkie tożsamości oparte na plikach cookie są ignorowane.

## <a name="selecting-the-scheme-with-policies"></a>Wybieranie schematu z zasadami

Jeśli wolisz określić żądane schematy w [zasadach](xref:security/authorization/policies), możesz ustawić `AuthenticationSchemes` kolekcję podczas dodawania zasad:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

W poprzednim przykładzie zasada "Over18" działa tylko w odniesieniu do tożsamości utworzonej przez procedurę obsługi "Bearer". Użyj zasad, ustawiając `[Authorize]` `Policy` właściwość atrybutu:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Używanie wielu schematów uwierzytelniania

Niektóre aplikacje mogą wymagać obsługi wielu typów uwierzytelniania. Na przykład aplikacja może uwierzytelniać użytkowników z Azure Active Directory i z bazy danych użytkowników. Innym przykładem jest aplikacja, która uwierzytelnia użytkowników zarówno z Active Directory Federation Services, jak i Azure Active Directory B2C. W takim przypadku aplikacja powinna akceptować token okaziciela JWT z kilku wystawców.

Dodaj wszystkie schematy uwierzytelniania, które chcesz zaakceptować. Na przykład poniższy kod w programie `Startup.ConfigureServices` dodaje dwa systemy uwierzytelniania okaziciela JWT z różnymi wystawcami:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> Zarejestrowano tylko jedno uwierzytelnianie okaziciela JWT z domyślnym schematem `JwtBearerDefaults.AuthenticationScheme`uwierzytelniania. Dodatkowe uwierzytelnianie musi być zarejestrowane przy użyciu unikatowego schematu uwierzytelniania.

Następnym krokiem jest zaktualizowanie domyślnych zasad autoryzacji w celu zaakceptowania obu schematów uwierzytelniania. Przykład:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

Ponieważ domyślne zasady autoryzacji są zastępowane, można użyć `[Authorize]` atrybutu w kontrolerach. Kontroler akceptuje żądania z tokenem JWT wystawionym przez pierwszego lub drugiego wystawcy.

::: moniker-end
