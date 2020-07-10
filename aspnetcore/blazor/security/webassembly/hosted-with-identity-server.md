---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core Identity z serwerem
author: guardrex
description: Aby utworzyć nową Blazor hostowaną aplikację z uwierzytelnianiem z poziomu programu Visual Studio, który używa zaplecza [ Identity serwera](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 1e5b4e37acd11280ec41c137426ecc4776d231be
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176247"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core Identity z serwerem

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

W tym artykule wyjaśniono, jak utworzyć nową Blazor hostowaną aplikację, która używa [ Identity serwera](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:

1. Po wybraniu szablonu ** Blazor WebAssembly aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.

1. Wybierz opcję **konta poszczególnych użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby przechowywać użytkowników w ramach aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core.

1. Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/interfejs wiersza polecenia platformy .NET Core](#tab/visual-studio-code+netcore-cli)

Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania w pustym folderze, określ `Individual` mechanizm uwierzytelniania z `-au|--auth` opcją zapisania użytkowników w aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Symbol zastępczy  | Przykład        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.

Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:

1. W kroku **Skonfiguruj nową Blazor WebAssembly aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .

1. Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core [Identity](xref:security/authentication/identity) .

1. Zaznacz pole wyboru **hostowane ASP.NET Core** .

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

  * IdentitySerwer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocnika, która konfiguruje domyślne konwencje ASP.NET Core na Identity serwerze:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez Identity serwer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* W pliku `Startup.Configure`:

  * IdentityOprogramowanie pośredniczące serwera uwidacznia punkty końcowe połączenia Open ID (OIDC):

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

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Metoda pomocnika konfiguruje [ Identity serwer](https://identityserver.io/) dla scenariuszy ASP.NET Core. IdentitySerwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji. IdentitySerwer uwidacznia niepotrzebną złożoność dla najbardziej typowych scenariuszy. W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia. Gdy uwierzytelnianie wymaga zmiany, Identity dostępna jest pełna moc serwera, aby dostosować uwierzytelnianie do wymagań aplikacji.

### <a name="addidentityserverjwt"></a>Dodaj Identity ServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania. Zasady są skonfigurowane tak, aby zezwalały Identity na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania. Ponadto ta metoda:

* Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `{APPLICATION NAME}API` .
* Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez Identity serwer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

W `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy. Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu. Domyślne zasady autoryzacji są skonfigurowane do używania domyślnego schematu uwierzytelniania, który jest konfigurowany przez program <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.

### <a name="applicationdbcontext"></a>ApplicationDbContext

W programie `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) program <xref:Microsoft.EntityFrameworkCore.DbContext> rozszerza, <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Aby uwzględnić schemat dla Identity serwera. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

W `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.

### <a name="app-settings"></a>Ustawienia aplikacji

W sekcji Ustawienia aplikacji ( `appsettings.json` ) w katalogu głównym projektu `IdentityServer` sekcja zawiera opis listy skonfigurowanych klientów. W poniższym przykładzie istnieje pojedynczy klient. Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth. Profil wskazuje konfigurowany typ aplikacji. Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

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

Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) w pliku projektu aplikacji. Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a>`HttpClient`skonfigurować

W programie `Program.Main` ( `Program.cs` ) nazwa <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) jest skonfigurowana do dostarczania <xref:System.Net.Http.HttpClient> wystąpień, które zawierają tokeny dostępu podczas wykonywania żądań do interfejsu API serwera:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Jeśli konfigurujesz Blazor WebAssembly aplikację tak, aby korzystała z istniejącego Identity wystąpienia serwera, które nie jest częścią Blazor rozwiązania hostowanego, Zmień <xref:System.Net.Http.HttpClient> rejestrację adresu podstawowego z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adres URL punktu końcowego autoryzacji interfejsu API aplikacji serwera.

### <a name="api-authorization-support"></a>Obsługa autoryzacji interfejsu API

Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pakiecie. Ta metoda konfiguruje usługi wymagane przez aplikację do współdziałania z istniejącym systemem autoryzacji.

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

`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:

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

## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom aplikację z projektu serwera. W przypadku korzystania z programu Visual Studio:

* Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .
* Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nazwa i rola roli z autoryzacją interfejsu API

### <a name="custom-user-factory"></a>Niestandardowa fabryka użytkowników

W aplikacji klienckiej Utwórz niestandardową fabrykę użytkowników. IdentitySerwer wysyła wiele ról jako tablicę JSON w ramach pojedynczego `role` żądania. Pojedyncza rola jest wysyłana jako wartość ciągu w ramach żądania. Fabryka tworzy pojedyncze zgłoszenie `role` dla każdej z ról użytkownika.

`CustomUserFactory.cs`:

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

W aplikacji klienckiej Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ):

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

`ProfileService.cs`:

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

* [ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )
* [Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )

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
* <xref:blazor/security/webassembly/additional-scenarios>
* [Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
