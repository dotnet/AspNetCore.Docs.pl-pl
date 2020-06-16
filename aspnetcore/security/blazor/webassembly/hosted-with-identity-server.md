---
title: Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly z Identity serwerem
author: guardrex
description: Aby utworzyć nową Blazor hostowaną aplikację z uwierzytelnianiem z poziomu programu Visual Studio, który używa zaplecza [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776517"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly z Identity serwerem

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

W tym artykule wyjaśniono, jak utworzyć nową Blazor hostowaną aplikację, która używa [IdentityServer](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W programie Visual Studio:

1. Utwórz nową aplikację ** Blazor webassembly** . Aby uzyskać więcej informacji, zobacz <xref:blazor/get-started>.
1. W oknie dialogowym **Tworzenie nowej Blazor aplikacji** wybierz pozycję **Zmień** w sekcji **uwierzytelnianie** .
1. Wybierz **pojedyncze konta użytkowników** , a następnie **przycisk OK**.
1. Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .
1. Wybierz przycisk **Utwórz**.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

Aby utworzyć aplikację w powłoce poleceń, wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample` ). Nazwa folderu jest również częścią nazwy projektu.

---

## <a name="server-app-configuration"></a>Konfiguracja aplikacji serwera

W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.

### <a name="startup-class"></a>Klasa początkowa

`Startup`Klasa zawiera następujące dodatki.

* W pliku `Startup.ConfigureServices`:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocnika, która konfiguruje domyślne konwencje ASP.NET Core na IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikowania tokenów JWT utworzonych przez IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* W pliku `Startup.Configure`:

  * Oprogramowanie pośredniczące IdentityServer uwidacznia punkty końcowe połączenia Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * Oprogramowanie pośredniczące uwierzytelniania jest odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:

    ```csharp
    app.UseAuthentication();
    ```

  * Oprogramowanie pośredniczące autoryzacji włącza funkcje autoryzacji:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Metoda pomocnika konfiguruje [IdentityServer](https://identityserver.io/) dla scenariuszy ASP.NET Core. IdentityServer to zaawansowane i rozszerzalne środowisko do obsługi zagadnień związanych z zabezpieczeniami aplikacji. IdentityServer ujawnia niepotrzebną złożoność w najbardziej typowych scenariuszach. W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia. Gdy uwierzytelnianie wymaga zmiany, dostępna jest pełna moc IdentityServer, aby dostosować uwierzytelnianie do wymagań aplikacji.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania. Zasady są skonfigurowane tak, aby zezwalały Identity na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania. Ponadto ta metoda:

* Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z IdentityServer z domyślnym zakresem `{APPLICATION NAME}API` .
* Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez IdentityServer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

W `WeatherForecastController` (*controllers/WeatherForecastController. cs*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy. Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu. Domyślne zasady autoryzacji są skonfigurowane do używania domyślnego schematu uwierzytelniania, który jest konfigurowany przez program <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.

### <a name="applicationdbcontext"></a>ApplicationDbContext

W programie `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) program <xref:Microsoft.EntityFrameworkCore.DbContext> rozszerza, <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Aby uwzględnić schemat dla IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

W `OidcConfigurationController` (*controllers/OidcConfigurationController. cs*) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.

### <a name="app-settings-files"></a>Pliki ustawień aplikacji

W sekcji Ustawienia aplikacji (*appsettings.json*) w katalogu głównym projektu `IdentityServer` sekcja zawiera opis listy skonfigurowanych klientów. W poniższym przykładzie istnieje pojedynczy klient. Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth. Profil wskazuje konfigurowany typ aplikacji. Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>Konfiguracja aplikacji klienta

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla pakietu [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) w pliku projektu aplikacji. Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Obsługa autoryzacji interfejsu API

Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w pakiecie [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Ta metoda konfiguruje usługi wymagane przez aplikację do współdziałania z istniejącym systemem autoryzacji.

```csharp
builder.Services.AddApiAuthorization();
```

Domyślnie konfiguracja aplikacji jest ładowana zgodnie z Konwencją z programu `_configuration/{client-id}` . Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji. Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.

### <a name="imports-file"></a>Importuje plik

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Składnik LoginDisplay

`LoginDisplay`Składnik (*Shared/LoginDisplay. Razor*) jest renderowany w `MainLayout` składniku (*Shared/MainLayout. Razor*) i zarządza następującymi zachowaniami:

* Dla uwierzytelnionych użytkowników:
  * Wyświetla bieżącą nazwę użytkownika.
  * Oferuje link do strony profilu użytkownika w ASP.NET Core Identity .
  * Oferuje przycisk umożliwiający wylogowanie się z aplikacji.
* Dla użytkowników anonimowych:
  * Oferuje opcję rejestracji.
  * Oferuje opcję logowania.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Składnik FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom aplikację z projektu serwera. W przypadku korzystania z programu Visual Studio:

* Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .
* Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nazwa i rola roli z autoryzacją interfejsu API

### <a name="custom-user-factory"></a>Niestandardowa fabryka użytkowników

W aplikacji klienckiej Utwórz niestandardową fabrykę użytkowników. IdentitySerwer wysyła wiele ról jako tablicę JSON w ramach pojedynczego `role` żądania. Pojedyncza rola jest wysyłana jako wartość ciągu w ramach żądania. Fabryka tworzy pojedyncze zgłoszenie `role` dla każdej z ról użytkownika.

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

W aplikacji klienckiej Zarejestruj fabrykę w `Program.Main` (*program.cs*):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

W aplikacji serwera Wywołaj <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity konstruktora, który dodaje usługi związane z rolami:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Skonfiguruj Identity serwer

Skorzystaj z **jednej** z następujących metod:

* [Opcje autoryzacji interfejsu API](#api-authorization-options)
* [Usługa profilowania](#profile-service)

#### <a name="api-authorization-options"></a>Opcje autoryzacji interfejsu API

W aplikacji serwerowej:

* Skonfiguruj Identity serwer, aby umieścić `name` oświadczenia i w tokenie `role` dostępu.
* Zapobiegaj domyślnym mapowaniu ról w programie obsługi tokenów JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Usługa profilowania

W aplikacji serwerowej Utwórz `ProfileService` implementację.

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

W aplikacji serwera Zarejestruj usługę profilu w `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Korzystanie z mechanizmów autoryzacji

W aplikacji klienckiej podejścia do autoryzacji składników są w tym momencie funkcjonalne. Każdy mechanizm autoryzacji w składnikach może używać roli do autoryzowania użytkownika:

* [AuthorizeView — składnik](xref:security/blazor/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` dyrektywa Attribute](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )
* [Logika proceduralna](xref:security/blazor/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )

  Obsługiwane są wiele testów ról:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`Program jest wypełniany w aplikacji klienckiej przy użyciu nazwy użytkownika, która jest zazwyczaj ich adresem e-mail logowania.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wdrożenie do Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importowanie certyfikatu z Key Vault (dokumentacja platformy Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
