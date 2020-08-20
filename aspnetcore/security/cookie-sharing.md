---
title: Udostępnianie uwierzytelniania cookie między aplikacjami ASP.NET
author: rick-anderson
description: Dowiedz się, jak udostępniać uwierzytelnianie cookie w aplikacjach ASP.NET 4. x i ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
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
uid: security/cookie-sharing
ms.openlocfilehash: 6ac808d11790ae27e82606b442ff215d95b93e41
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631371"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Udostępnianie uwierzytelniania cookie między aplikacjami ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Witryny sieci Web często składają się ze współpracujących aplikacji sieci Web. Aby zapewnić obsługę logowania jednokrotnego, aplikacje sieci Web w ramach lokacji muszą udostępniać uwierzytelnianie cookie s. Aby obsłużyć ten scenariusz, stos ochrony danych umożliwia udostępnianie Katana cookie uwierzytelniania i ASP.NET Core cookie biletów uwierzytelniania.

W następujących przykładach:

* Nazwa uwierzytelniania cookie ma ustawioną wspólną wartość `.AspNet.SharedCookie` .
* `AuthenticationType`Jest ustawiona `Identity.Application` jawnie lub domyślnie.
* Nazwa wspólnej aplikacji jest używana w celu umożliwienia systemowi ochrony danych udostępniania kluczy ochrony danych ( `SharedCookieApp` ).
* `Identity.Application` jest używany jako schemat uwierzytelniania. Niezależnie od tego, jaki schemat jest używany, musi być używany spójnie *w ramach i między* udostępnionymi cookie aplikacjami jako schemat domyślny lub przez jawne ustawienie go. Schemat jest używany podczas szyfrowania i odszyfrowywania cookie , więc w aplikacjach musi być używany spójny schemat.
* Używana jest wspólna lokalizacja magazynu [kluczy ochrony danych](xref:security/data-protection/implementation/key-management) .
  * W ASP.NET Core aplikacje <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> służy do ustawiania lokalizacji magazynu kluczy.
  * W aplikacjach .NET Framework, Cookie oprogramowanie pośredniczące uwierzytelniania korzysta z implementacji programu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` zapewnia usługi ochrony danych do szyfrowania i odszyfrowywania cookie danych ładunku uwierzytelniania. `DataProtectionProvider`Wystąpienie jest odizolowane od systemu ochrony danych używanego przez inne części aplikacji. [DataProtectionProvider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) przyjmuje wartość <xref:System.IO.DirectoryInfo> określającą lokalizację magazynu kluczy ochrony danych.
* `DataProtectionProvider` wymaga pakietu NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * W ASP.NET Core 2. x aplikacje odwołują się do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * W .NET Framework aplikacje Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Ustawia wspólną nazwę aplikacji.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Udostępnianie uwierzytelniania cookie przy użyciu ASP.NET Core Identity

W przypadku korzystania z ASP.NET Core Identity :

* Klucze ochrony danych i nazwa aplikacji muszą być udostępniane między aplikacjami. W poniższych przykładach udostępniono wspólną lokalizację magazynu kluczy <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> . Użyj, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Aby skonfigurować wspólną nazwę udostępnionej aplikacji ( `SharedCookieApp` w poniższych przykładach). Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/configuration/overview>.
* Użyj <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metody rozszerzenia, aby skonfigurować usługę ochrony danych dla cookie s.
* Domyślny typ uwierzytelniania to `Identity.Application` .

W pliku `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Udostępnianie uwierzytelniania cookie bez ASP.NET Core Identity

W przypadku korzystania z usługi cookie s bezpośrednio ASP.NET Core Identity , w programie należy skonfigurować ochronę danych i uwierzytelnianie `Startup.ConfigureServices` . W poniższym przykładzie typ uwierzytelniania jest ustawiony na `Identity.Application` :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a>Udostępnianie zasobów cookie między różnymi ścieżkami podstawowymi

Uwierzytelnianie cookie domyślnie używa elementu [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Ścieżka](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Jeśli aplikacja cookie musi być współużytkowana przez różne ścieżki podstawowe, `Path` należy ją zastąpić:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Udostępnianie cookie s w poddomenach

W przypadku hostowania aplikacji, które współużytkują cookie elementy w poddomenach, należy określić wspólną domenę w [ Cookie . Właściwość domeny](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Aby udostępnić cookie aplikacje w aplikacjach `contoso.com` , takich jak `first_subdomain.contoso.com` i `second_subdomain.contoso.com` , określ `Cookie.Domain` jako `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Szyfruj klucze ochrony danych w spoczynku

W przypadku wdrożeń produkcyjnych Skonfiguruj `DataProtectionProvider` do szyfrowania klucze przechowywane przy użyciu funkcji DPAPI lub x509. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/implementation/key-encryption-at-rest>. W poniższym przykładzie podano odcisk palca certyfikatu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Udostępnianie uwierzytelniania cookie s między ASP.NET. x a aplikacjami ASP.NET Core

ASP.NET 4. x aplikacje używające Cookie oprogramowania pośredniczącego uwierzytelniania Katana można skonfigurować tak, aby generowały uwierzytelnianie cookie , które są zgodne z programem Cookie pośredniczącym uwierzytelniania ASP.NET Core. Dzięki temu można uaktualnić poszczególne aplikacje dużej witryny w kilku krokach, jednocześnie zapewniając bezproblemowe środowisko logowania jednokrotnego w całej lokacji.

Gdy aplikacja używa Cookie oprogramowania pośredniczącego uwierzytelniania Katana, wywołuje `UseCookieAuthentication` plik *Startup.auth.cs* projektu. Projekty aplikacji sieci Web ASP.NET 4. x utworzone przy użyciu Visual Studio 2013 i później domyślnie korzystają z Cookie oprogramowania pośredniczącego uwierzytelniania Katana. Chociaż `UseCookieAuthentication` jest przestarzała i nieobsługiwana w przypadku aplikacji ASP.NET Core, wywołanie `UseCookieAuthentication` w aplikacji ASP.NET 4. x, która używa Cookie oprogramowania pośredniczącego uwierzytelniania Katana.

Aplikacja ASP.NET 4. x musi być docelowa .NET Framework 4.5.1 lub nowsza. W przeciwnym razie instalacja niezbędnych pakietów NuGet nie powiodła się.

Aby udostępnić uwierzytelnianie cookie s między aplikacją ASP.NET 4. x a aplikacją ASP.NET Core, skonfiguruj aplikację ASP.NET Core zgodnie z opisem w sekcji [udostępnianie uwierzytelniania w cookie różnych ASP.NET Core aplikacjach](#share-authentication-cookies-with-aspnet-core-identity) , a następnie skonfiguruj aplikację ASP.NET 4. x w następujący sposób.

Upewnij się, że pakiety aplikacji zostały zaktualizowane do najnowszej wersji. Zainstaluj pakiet [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) w każdej aplikacji ASP.NET 4. x.

Znajdź i zmodyfikuj wywołanie `UseCookieAuthentication` :

* Zmień nazwę tak, cookie aby odpowiadała nazwie używanej przez Cookie oprogramowanie pośredniczące uwierzytelniania ASP.NET Core ( `.AspNet.SharedCookie` w tym przykładzie).
* W poniższym przykładzie typ uwierzytelniania jest ustawiony na `Identity.Application` .
* Podaj wystąpienie `DataProtectionProvider` zainicjowane do lokalizacji magazynu kluczy Common Data Protection.
* Upewnij się, że nazwa aplikacji jest ustawiona na wspólną nazwę aplikacji używaną przez wszystkie aplikacje, które współużytkują uwierzytelnianie cookie s ( `SharedCookieApp` w tym przykładzie).

Jeśli to nie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` jest ustawienie i `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , Ustaw jako <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> zastrzeżenie odróżniające unikatowych użytkowników.

*/Startup.auth.cs App_Start*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Podczas generowania tożsamości użytkownika typ uwierzytelniania ( `Identity.Application` ) musi być zgodny z typem zdefiniowanym w elemencie `AuthenticationType` `UseCookieAuthentication` w *App_Start/Startup.auth.cs*.

*Modele/ Identity Models.cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Używanie wspólnej bazy danych użytkownika

Gdy aplikacje korzystają z tego samego Identity schematu (w tej samej wersji programu Identity ), upewnij się, że Identity system dla każdej aplikacji jest wskazany w tej samej bazie danych użytkownika. W przeciwnym razie system tożsamości generuje błędy w czasie wykonywania, gdy próbuje dopasować informacje w ramach uwierzytelniania cookie względem informacji w swojej bazie danych.

Gdy Identity schemat różni się między aplikacjami, zwykle ponieważ aplikacje korzystają z różnych Identity wersji, udostępnianie wspólnej bazy danych opartej na najnowszej wersji programu Identity nie jest możliwe bez ponownego mapowania i dodawania kolumn w Identity schematach innych aplikacji. Jest to często bardziej wydajne, aby uaktualnić inne aplikacje w celu korzystania z najnowszej wersji, aby Identity Wspólna baza danych mogła być współużytkowana przez aplikacje.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:host-and-deploy/web-farm>
