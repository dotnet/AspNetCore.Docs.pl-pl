---
title: Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0
author: scottaddie
description: W tym artykule opisano najczęstsze kroki migracji ASP.NET Core 1. x oraz Identity do ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057183"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a>Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0

Przez [Scott Addie](https://github.com/scottaddie) i [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 ma nowy model uwierzytelniania i [Identity](xref:security/authentication/identity) upraszcza konfigurację przy użyciu usług. ASP.NET Core 1. x aplikacji korzystających z uwierzytelniania lub Identity można je zaktualizować, aby użyć nowego modelu, jak opisano poniżej.

## <a name="update-namespaces"></a>Aktualizowanie przestrzeni nazw

W 1. x klasy takie jak `IdentityRole` i `IdentityUser` zostały znalezione w `Microsoft.AspNetCore.Identity.EntityFrameworkCore` przestrzeni nazw.

W 2,0, <xref:Microsoft.AspNetCore.Identity> przestrzeń nazw stało się nowym domem dla kilku takich klas. W przypadku domyślnego Identity kodu klasy, których to dotyczy, obejmują `ApplicationUser` i `Startup` . Dostosuj swoje `using` instrukcje, aby rozwiązać odnośne odwołania.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Uwierzytelnianie i oprogramowanie pośredniczące

W projektach 1. x uwierzytelnianie jest konfigurowane za pośrednictwem oprogramowania pośredniczącego. Metoda pośrednicząca jest wywoływana dla każdego schematu uwierzytelniania, który ma być obsługiwany.

Poniższy przykład 1. x konfiguruje uwierzytelnianie w serwisie Facebook Identity w programie *Startup.cs* :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

W przypadku projektów 2,0 uwierzytelnianie jest konfigurowane za pośrednictwem usług. Każdy schemat uwierzytelniania jest rejestrowany w `ConfigureServices` metodzie *Startup.cs* . `UseIdentity`Metoda jest zastępowana przez `UseAuthentication` .

Poniższy przykład 2,0 służy do konfigurowania uwierzytelniania w serwisie Facebook Identity w programie *Startup.cs* :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

`UseAuthentication`Metoda dodaje pojedynczy składnik pośredniczący uwierzytelniania, który jest odpowiedzialny za automatyczne uwierzytelnianie i obsługę żądań uwierzytelniania zdalnego. Zastępuje wszystkie poszczególne składniki pośredniczące pojedynczym, wspólnym składnikiem pośredniczącym.

Poniżej przedstawiono 2,0 instrukcje dotyczące migracji dla każdego głównego schematu uwierzytelniania.

### <a name="no-loccookie-based-authentication"></a>Cookieuwierzytelnianie oparte na usłudze

Wybierz jedną z dwóch opcji poniżej i wprowadź niezbędne zmiany w programie *Startup.cs* :

1. Użyj cookie s z Identity
    - Zamień `UseIdentity` na `UseAuthentication` w `Configure` metodzie:

        ```csharp
        app.UseAuthentication();
        ```

    - Wywołaj `AddIdentity` metodę w `ConfigureServices` metodzie, aby dodać cookie usługi uwierzytelniania.
    - Opcjonalnie Wywołaj `ConfigureApplicationCookie` metodę lub `ConfigureExternalCookie` w `ConfigureServices` metodzie, aby dostosować Identity cookie Ustawienia.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Użyj cookie bez Identity
    - Zastąp `UseCookieAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

        ```csharp
        app.UseAuthentication();
        ```

    - Wywołaj `AddAuthentication` `AddCookie` metody i w `ConfigureServices` metodzie:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Uwierzytelnianie okaziciela JWT

Wprowadź następujące zmiany w programie *Startup.cs* :
- Zastąp `UseJwtBearerAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Wywołaj `AddJwtBearer` metodę w `ConfigureServices` metodzie:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Ten fragment kodu nie używa Identity , dlatego należy ustawić domyślny schemat, przekazując `JwtBearerDefaults.AuthenticationScheme` do `AddAuthentication` metody.

### <a name="openid-connect-oidc-authentication"></a>Uwierzytelnianie OpenID Connect (OIDC)

Wprowadź następujące zmiany w programie *Startup.cs* :

- Zastąp `UseOpenIdConnectAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Wywołaj `AddOpenIdConnect` metodę w `ConfigureServices` metodzie:

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- Zastąp `PostLogoutRedirectUri` Właściwość w `OpenIdConnectOptions` akcji `SignedOutRedirectUri` :

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Uwierzytelnianie przy użyciu usługi Facebook

Wprowadź następujące zmiany w programie *Startup.cs* :
- Zastąp `UseFacebookAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Wywołaj `AddFacebook` metodę w `ConfigureServices` metodzie:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Uwierzytelnianie przy użyciu usługi Google

Wprowadź następujące zmiany w programie *Startup.cs* :
- Zastąp `UseGoogleAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Wywołaj `AddGoogle` metodę w `ConfigureServices` metodzie:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Uwierzytelnianie za pomocą konta Microsoft

Aby uzyskać więcej informacji na temat uwierzytelniania konto Microsoft, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/14455)w usłudze GitHub.

Wprowadź następujące zmiany w programie *Startup.cs* :
- Zastąp `UseMicrosoftAccountAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Wywołaj `AddMicrosoftAccount` metodę w `ConfigureServices` metodzie:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Uwierzytelnianie przy użyciu usługi Twitter

Wprowadź następujące zmiany w programie *Startup.cs* :
- Zastąp `UseTwitterAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Wywołaj `AddTwitter` metodę w `ConfigureServices` metodzie:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Ustawianie domyślnych schematów uwierzytelniania

W 1. x, `AutomaticAuthenticate` właściwości i `AutomaticChallenge` klasy bazowej [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) zostały przeznaczone do ustawienia w jednym schemacie uwierzytelniania. Nie było dobrym sposobem wymuszenia tego.

W 2,0 te dwie właściwości zostały usunięte jako właściwości w poszczególnych `AuthenticationOptions` wystąpieniach. Można je skonfigurować w `AddAuthentication` wywołaniu metody w `ConfigureServices` metodzie *Startup.cs* :

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

W poprzednim fragmencie kodu domyślny schemat jest ustawiany na `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Alternatywnie można użyć przeciążonej wersji `AddAuthentication` metody do ustawienia więcej niż jednej właściwości. W poniższym przykładzie przeciążonej metody domyślny schemat jest ustawiany na `CookieAuthenticationDefaults.AuthenticationScheme` . Schemat uwierzytelniania można także określić w ramach poszczególnych `[Authorize]` atrybutów lub zasad autoryzacji.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Zdefiniuj domyślny schemat w 2,0, jeśli spełniony jest jeden z następujących warunków:
- Chcesz, aby użytkownik był zalogowany automatycznie
- Używasz `[Authorize]` zasad atrybutu lub autoryzacji bez określania schematów

Wyjątkiem od tej reguły jest `AddIdentity` Metoda. Ta metoda dodaje cookie do użytkownika i ustawia domyślne schematy uwierzytelniania i wyzwania dla aplikacji cookie `IdentityConstants.ApplicationScheme` . Ponadto ustawia domyślny schemat logowania na zewnętrzny cookie `IdentityConstants.ExternalScheme` .

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Korzystanie z rozszerzeń kontekstu uwierzytelniania

`IAuthenticationManager`Interfejs jest głównym punktem wejścia do systemu uwierzytelniania 1. x. Został on zastąpiony nowym zestawem `HttpContext` metod rozszerzających w `Microsoft.AspNetCore.Authentication` przestrzeni nazw.

Na przykład projekty 1. x odwołują się do `Authentication` Właściwości:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

W projektach 2,0, zaimportuj `Microsoft.AspNetCore.Authentication` przestrzeń nazw i Usuń `Authentication` odwołania do właściwości:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Uwierzytelnianie systemu Windows (HTTP.sys/IISIntegration)

Istnieją dwie różne warianty uwierzytelniania systemu Windows:

* Host zezwala tylko uwierzytelnionym użytkownikom. Ta zmiana nie ma wpływ na 2,0 zmian.
* Host umożliwia zarówno anonimowych, jak i uwierzytelnionych użytkowników. Ta zmiana ma wpływ na zmiany 2,0. Na przykład aplikacja powinna zezwalać na anonimowych użytkowników w warstwie [usług IIS](xref:host-and-deploy/iis/index) lub [HTTP.sys](xref:fundamentals/servers/httpsys) , ale autoryzuje użytkowników na poziomie kontrolera. W tym scenariuszu Ustaw domyślny schemat w `Startup.ConfigureServices` metodzie.

  Dla elementu [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)Ustaw domyślny schemat na `IISDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Dla elementu [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)Ustaw domyślny schemat na `HttpSysDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Nie można ustawić domyślnego schematu, aby zapobiec działaniu żądania Autoryzuj (wyzwanie) z powodu następującego wyjątku:

  > `System.InvalidOperationException`: Nie określono authenticationScheme i nie znaleziono DefaultChallengeScheme.

Aby uzyskać więcej informacji, zobacz <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a>IdentityCookieWystąpienia opcji

Efektem ubocznym zmian 2,0 jest przełączenie do użycia nazwanych opcji zamiast cookie wystąpień opcji. Możliwość dostosowywania Identity cookie nazw schematów jest usuwana.

Na przykład projekty 1. x wykorzystują [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) do przekazania `IdentityCookieOptions` parametru do *AccountController.cs* i *ManageController.cs* . Zewnętrzny cookie schemat uwierzytelniania jest dostępny z podanego wystąpienia:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Wspomniane wyżej iniekcja konstruktora nie jest konieczna w projektach 2,0 i `_externalCookieScheme` można usunąć pole:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1. x projekty używały `_externalCookieScheme` pola w następujący sposób:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

W projektach 2,0 Zastąp poprzedni kod poniższym kodem. `IdentityConstants.ExternalScheme`Stała może być używana bezpośrednio.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Usuń nowo dodane `SignOutAsync` wywołanie, importując następującą przestrzeń nazw:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a>Dodawanie Identity właściwości nawigacji poco użytkownika

Wszystkie podstawowe właściwości nawigacji Entity Framework (EF) podstawowego `IdentityUser` poco (stary obiekt CLR) zostały usunięte. Jeśli projekt 1. x użył tych właściwości, ręcznie dodaj je z powrotem do projektu 2,0:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Aby zapobiec duplikowaniu kluczy obcych podczas uruchamiania EF Core migracji, Dodaj następujące elementy do `IdentityDbContext` `OnModelCreating` metody klasy (po `base.OnModelCreating();` wywołaniu):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Zastąp GetExternalAuthenticationSchemes

Metoda synchroniczna `GetExternalAuthenticationSchemes` została usunięta na korzyść asynchronicznej wersji. 1. x projekty mają następujący kod w obszarze *controllers/ManageController. cs* :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Ta metoda pojawia się w obszarze *widoki/Account/Login. cshtml* :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

W projektach 2,0 użyj <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metody. Zmiana w *ManageController.cs* przypomina następujący kod:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

W polu *login. cshtml* właściwość, do której `AuthenticationScheme` uzyskuje się dostęp w `foreach` pętli, zmieni się na `Name` :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Zmiana właściwości ManageLoginsViewModel

`ManageLoginsViewModel`Obiekt jest używany w `ManageLogins` akcji *ManageController.cs* . W projektach 1. x `OtherLogins` Typ zwracany właściwości obiektu to `IList<AuthenticationDescription>` . Ten typ zwracany wymaga importu `Microsoft.AspNetCore.Http.Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

W projektach 2,0 typ zwracany zmieni się na `IList<AuthenticationScheme>` . Ten nowy typ zwracany wymaga zastąpienia importu importem `Microsoft.AspNetCore.Http.Authentication` `Microsoft.AspNetCore.Authentication` .

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz [Omówienie problemu z uwierzytelnianiem 2,0](https://github.com/aspnet/Security/issues/1338) w witrynie GitHub.
