---
title: Zabezpieczanie hostowanej Blazor WebAssembly aplikacji ASP.NET Core z Identity serwerem
author: guardrex
description: Dowiedz się, jak zabezpieczyć hostowaną Blazor WebAssembly aplikację ASP.NET Core z Identity serwerem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 147f1d6cdea0b9992b8be333db4cb06e30c7feaf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055220"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core Identity z serwerem

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

W tym artykule wyjaśniono, jak utworzyć [hostowaną Blazor WebAssembly aplikację](xref:blazor/hosting-models#blazor-webassembly) , która używa [ Identity serwera](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API.

> [!NOTE]
> Aby skonfigurować autonomiczną lub hostowaną Blazor WebAssembly aplikację do korzystania z istniejącego Identity wystąpienia serwera zewnętrznego, postępuj zgodnie ze wskazówkami w temacie <xref:blazor/security/webassembly/standalone-with-authentication-library> .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:

1. Po wybraniu szablonu **Blazor WebAssembly aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie** .

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

## <a name="server-app-configuration"></a>*`Server`* Konfiguracja aplikacji

W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.

### <a name="startup-class"></a>Klasa początkowa

`Startup`Klasa zawiera następujące dodatki.

* W pliku `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

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

  * IdentityOprogramowanie pośredniczące serwera uwidacznia punkty końcowe OpenID Connect Connect (OIDC):

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

### <a name="addno-locidentityserverjwt"></a>Dodaj Identity ServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania. Zasady są skonfigurowane tak, aby zezwalały Identity na obsługę wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Obsługuje wszystkie inne żądania. Ponadto ta metoda:

* Rejestruje `{APPLICATION NAME}API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `{APPLICATION NAME}API` .
* Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez Identity serwer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

W `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy. Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu. Domyślne zasady autoryzacji są skonfigurowane do używania domyślnego schematu uwierzytelniania, który jest konfigurowany przez program <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.

### <a name="applicationdbcontext"></a>ApplicationDbContext

W programie `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) program <xref:Microsoft.EntityFrameworkCore.DbContext> rozszerza, <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> Aby uwzględnić schemat dla Identity serwera. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

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

## <a name="client-app-configuration"></a>*`Client`* Konfiguracja aplikacji

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników ( `Individual` ), aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) w pliku projektu aplikacji. Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

### <a name="httpclient-configuration"></a>`HttpClient` skonfigurować

W programie `Program.Main` ( `Program.cs` ) nazwa <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) jest skonfigurowana do dostarczania <xref:System.Net.Http.HttpClient> wystąpień, które zawierają tokeny dostępu podczas wykonywania żądań do interfejsu API serwera:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Jeśli konfigurujesz Blazor WebAssembly aplikację tak, aby korzystała z istniejącego Identity wystąpienia serwera, które nie jest częścią rozwiązania hostowanego Blazor , Zmień <xref:System.Net.Http.HttpClient> rejestrację adresu podstawowego z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adres URL punktu końcowego autoryzacji interfejsu API aplikacji serwera.

### <a name="api-authorization-support"></a>Obsługa autoryzacji interfejsu API

Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakiecie. Ta metoda konfiguruje usługi wymagane przez aplikację do współdziałania z istniejącym systemem autoryzacji.

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
  * Oferuje link do strony profilu użytkownika w programie ASP.NET Core Identity .
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

W *`Client`* aplikacji Utwórz niestandardową fabrykę użytkowników. Identity Serwer wysyła wiele ról jako tablicę JSON w ramach pojedynczego `role` żądania. Pojedyncza rola jest wysyłana jako wartość ciągu w ramach żądania. Fabryka tworzy pojedyncze zgłoszenie `role` dla każdej z ról użytkownika.

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
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

W *`Client`* aplikacji Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

W *`Server`* aplikacji Zadzwoń do <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity konstruktora, który dodaje usługi związane z rolami:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>Skonfiguruj Identity serwer

Skorzystaj z **jednej** z następujących metod:

* [Opcje autoryzacji interfejsu API](#api-authorization-options)
* [Usługa profilowania](#profile-service)

#### <a name="api-authorization-options"></a>Opcje autoryzacji interfejsu API

W *`Server`* aplikacji:

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

W *`Server`* aplikacji Utwórz `ProfileService` implementację.

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

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

W *`Server`* aplikacji Zarejestruj usługę profilu w `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Korzystanie z mechanizmów autoryzacji

W *`Client`* aplikacji metody autoryzacji składników są w tym momencie funkcjonalne. Każdy mechanizm autoryzacji w składnikach może używać roli do autoryzowania użytkownika:

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

`User.Identity.Name` jest wypełniana w *`Client`* aplikacji nazwą użytkownika, która jest zazwyczaj ich adresem e-mail logowania.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a>Host w Azure App Service z domeną niestandardową

Poniższe wskazówki wyjaśniają sposób wdrażania hostowanej Blazor WebAssembly aplikacji na Identity serwerze w celu [Azure App Service](https://azure.microsoft.com/services/app-service/) z domeną niestandardową.

W tym scenariuszu hostingu **nie** należy używać tego samego certyfikatu dla [ Identity klucza podpisywania tokenu serwera](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) i bezpiecznej komunikacji protokołu HTTPS lokacji z przeglądarkami:

* Korzystanie z różnych certyfikatów dla tych dwóch wymagań jest dobrym sposobem na bezpieczeństwo, ponieważ izoluje klucze prywatne do każdego celu.
* Certyfikaty TLS do komunikacji z przeglądarkami są zarządzane niezależnie bez wpływu na Identity Podpisywanie tokenu serwera.
* Gdy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dostarcza certyfikat do aplikacji App Service dla niestandardowego powiązania domeny, Identity serwer nie może uzyskać tego samego certyfikatu z Azure Key Vault na potrzeby podpisywania tokenu. Chociaż skonfigurowanie Identity serwera do korzystania z tego samego certyfikatu TLS z ścieżki fizycznej jest możliwe, umieszczenie certyfikatów zabezpieczeń w kontroli źródła jest **słabo praktyczne i należy je unikać w większości scenariuszy** .

W poniższych wskazówkach certyfikat z podpisem własnym jest tworzony w Azure Key Vault wyłącznie dla Identity podpisywania tokenu serwera. IdentityKonfiguracja serwera używa certyfikatu magazynu kluczy za pośrednictwem `My`  >  `CurrentUser` magazynu certyfikatów aplikacji. Inne certyfikaty używane na potrzeby ruchu HTTPS z domenami niestandardowymi są tworzone i konfigurowane niezależnie od Identity certyfikatu podpisywania serwera.

Aby skonfigurować aplikację, Azure App Service i Azure Key Vault do hostowania przy użyciu domeny niestandardowej i protokołu HTTPS:

1. Utwórz [plan App Service](/azure/app-service/overview-hosting-plans) z poziomem planu `Basic B1` lub wyższym. `Basic B1`Aby można było używać domen niestandardowych, App Service wymaga lub wyższej warstwy usług.
1. Utwórz certyfikat PFX dla bezpiecznej komunikacji w przeglądarce (protokołu HTTPS) dla witryny o wspólnej nazwie w pełni kwalifikowanej nazwie domeny (FQDN) witryny, która kontroluje Twoja organizacja (na przykład `www.contoso.com` ). Utwórz certyfikat przy użyciu:
   * Użycie klucza
     * Weryfikacja podpisu cyfrowego ( `digitalSignature` )
     * Szyfrowanie klucza ( `keyEncipherment` )
   * Ulepszone/rozszerzone użycie klucza
     * Uwierzytelnianie klienta (1.3.6.1.5.5.7.3.2)
     * Uwierzytelnianie serwera (1.3.6.1.5.5.7.3.1)

   Aby utworzyć certyfikat, należy użyć jednej z następujących metod lub innego odpowiedniego narzędzia lub usługi online:

   * [Azure Key Vault](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [MakeCert w systemie Windows](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Zanotuj hasło, które zostanie użyte później do zaimportowania certyfikatu do Azure Key Vault.

   Aby uzyskać więcej informacji na temat Azure Key Vault certyfikatów, zobacz [Azure Key Vault: Certificates](/azure/key-vault/certificates/).
1. Utwórz nowy Azure Key Vault lub Użyj istniejącego magazynu kluczy w subskrypcji platformy Azure.
1. W obszarze **Certyfikaty** magazynu kluczy zaimportuj certyfikat lokacji PFX. Zanotuj odcisk palca certyfikatu, który jest używany później w konfiguracji aplikacji.
1. W Azure Key Vault Wygeneruj nowy certyfikat z podpisem własnym na potrzeby Identity podpisywania tokenu serwera. Nadaj certyfikatowi nazwę i **temat** **certyfikatu** . **Podmiot** jest określony jako `CN={COMMON NAME}` , gdzie `{COMMON NAME}` symbol zastępczy jest nazwą pospolitą certyfikatu. Nazwa pospolita może być dowolnym ciągiem alfanumerycznym. Na przykład `CN=IdentityServerSigning` jest prawidłowy **podmiot** certyfikatu. Użyj domyślnych ustawień **konfiguracji zaawansowanych zasad** . Zanotuj odcisk palca certyfikatu, który jest używany później w konfiguracji aplikacji.
1. Przejdź do Azure App Service w Azure Portal i Utwórz nowy App Service z następującą konfiguracją:
   * **Publikuj** ustawiony na `Code` .
   * **Stos środowiska uruchomieniowego** został ustawiony na środowisko uruchomieniowe aplikacji.
   * W polu **jednostka SKU i rozmiar** upewnij się, że warstwa App Service jest `Basic B1` lub wyższa.  `Basic B1`Aby można było używać domen niestandardowych, App Service wymaga lub wyższej warstwy usług.
1. Po utworzeniu App Service przez platformę Azure Otwórz **konfigurację** aplikacji i Dodaj nowe ustawienie aplikacji określające wcześniej zarejestrowane odciski palców certyfikatu. Klucz ustawienia aplikacji to `WEBSITE_LOAD_CERTIFICATES` . Oddziel odciski palców certyfikatu w wartości ustawienia aplikacji na przecinek, jak pokazano na poniższym przykładzie:
   * Klucz: `WEBSITE_LOAD_CERTIFICATES`
   * Wartość: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   W Azure Portal Zapisywanie ustawień aplikacji jest procesem dwuetapowym: Zapisz `WEBSITE_LOAD_CERTIFICATES` ustawienie klucz-wartość, a następnie wybierz przycisk **Zapisz** w górnej części bloku.
1. Wybierz **Ustawienia protokołu TLS/SSL** aplikacji. Wybierz pozycję **certyfikaty kluczy prywatnych (pfx)** . W celu zaimportowania certyfikatu lokacji na potrzeby komunikacji przy użyciu protokołu HTTPS i certyfikatu podpisywania serwera z podpisem własnym należy użyć dwa razy **zaimportuj certyfikat Key Vault** Identity .
1. Przejdź do bloku **domeny niestandardowe** . W witrynie sieci Web rejestratora domen Użyj **adresu IP** i **identyfikatora weryfikacji domeny niestandardowej** w celu skonfigurowania domeny. Typowa konfiguracja domeny obejmuje:
   * **Rekord a** **hosta** `@` i wartość adresu IP z Azure Portal.
   * **Rekord TXT** z **hostem** `asuid` i wartością identyfikatora weryfikacyjnego wygenerowaną przez platformę Azure i udostępnianą przez Azure Portal.

   Upewnij się, że poprawnie Zapisano zmiany w witrynie sieci Web rejestratora domen. Niektóre witryny sieci Web rejestratorów wymagają dwuetapowego procesu zapisywania rekordów domeny: co najmniej jeden rekord jest zapisywany osobno, a następnie przez aktualizację rejestracji domeny za pomocą oddzielnego przycisku.
1. Wróć do bloku **domen niestandardowych** w Azure Portal. Wybierz pozycję **Dodaj domenę niestandardową** . Wybierz opcję **rekordu A** . Podaj domenę i wybierz pozycję **Weryfikuj** . Jeśli rekordy domeny są poprawne i propagowane przez Internet, Portal umożliwia wybranie przycisku **Dodaj domenę niestandardową** .

   Zmiana rejestracji domeny w ramach serwerów nazw domen internetowych (DNS) po ich przetworzeniu przez rejestrator domen może potrwać kilka dni. Jeśli rekordy domeny nie są aktualizowane w ciągu trzech dni roboczych, upewnij się, że rekordy są poprawnie ustawione przy użyciu rejestratora domen i skontaktuj się z działem obsługi klienta.
1. W bloku **domeny niestandardowe** **stan protokołu SSL** dla domeny jest oznaczony `Not Secure` . Wybierz łącze **Dodaj powiązanie** . Wybierz certyfikat HTTPS lokacji z magazynu kluczy dla niestandardowego powiązania domeny.
1. W programie Visual Studio Otwórz plik ustawień aplikacji projektu *serwera* ( `appsettings.json` lub `appsettings.Production.json` ). W Identity konfiguracji serwera Dodaj następującą `Key` sekcję. Określ **podmiot** certyfikatu z podpisem własnym dla `Name` klucza. W poniższym przykładzie nazwa pospolita certyfikatu przypisana w magazynie kluczy to `IdentityServerSigning` , która daje **temat** `CN=IdentityServerSigning` :

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. W programie Visual Studio Utwórz [profil publikowania](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Azure App Service dla projektu *serwera* . Na pasku menu wybierz kolejno opcje: **Kompiluj**  >  **Publikuj**  >  **nowe**  >  **Azure App Service platformy Azure**  >  **Azure App Service** (system Windows lub Linux). Gdy program Visual Studio jest połączony z subskrypcją platformy Azure, można ustawić **Widok** zasobów platformy Azure według **typu zasobu** . Przejdź na listę **aplikacji sieci Web** , aby znaleźć App Service aplikacji i wybierz ją. Wybierz pozycję **Zakończ** .
1. Gdy program Visual Studio powróci do okna **publikowania** , automatycznie wykrywane są zależności usługi key i SQL Server Database.

   W przypadku usługi magazynu kluczy nie są wymagane żadne zmiany w konfiguracji domyślnej.

   W celach testowych lokalna baza danych programu [SQLite](https://www.sqlite.org/index.html) , która jest konfigurowana domyślnie przez Blazor szablon, może być wdrażana z aplikacją bez dodatkowej konfiguracji. Konfigurowanie innej bazy danych Identity serwera w środowisku produkcyjnym wykracza poza zakres tego artykułu. Aby uzyskać więcej informacji, zobacz zasoby bazy danych w następujących zestawach dokumentacji:
   * [App Service](/azure/app-service/)
   * [Identity Server](https://identityserver4.readthedocs.io/en/latest/)

1. Wybierz łącze **Edytuj** pod nazwą profilu wdrożenia w górnej części okna. Zmień docelowy adres URL na adres URL domeny niestandardowej witryny (na przykład `https://www.contoso.com` ). Zapisz ustawienia.
1. Opublikuj aplikację. Program Visual Studio otwiera okno przeglądarki i żąda lokacji w swojej domenie niestandardowej.

Dokumentacja platformy Azure zawiera dodatkowe szczegóły dotyczące korzystania z usług platformy Azure i domen niestandardowych z powiązaniem TLS w App Service, w tym informacje na temat używania rekordów CNAME zamiast rekordów. Więcej informacji można znaleźć w następujących zasobach:

* [Dokumentacja App Service](/azure/app-service/)
* [Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](/azure/app-service/configure-ssl-bindings)
* [Azure Key Vault](/azure/key-vault/)

Zalecamy używanie nowego okna przeglądarki w trybie prywatnym lub incognito dla każdego przebiegu testu aplikacji po zmianie aplikacji, konfiguracji aplikacji lub usług platformy Azure w Azure Portal. cookieWyzwolenie z poprzedniego przebiegu testowego może spowodować niepowodzenie uwierzytelniania lub autoryzacji podczas testowania lokacji nawet wtedy, gdy konfiguracja lokacji jest poprawna. Aby uzyskać więcej informacji na temat sposobu konfigurowania programu Visual Studio, aby otworzyć nowe okno przeglądarki w trybie prywatnym lub incognito dla każdego przebiegu testowego, zobacz sekcję [ Cookie s i dane lokacji](#cookies-and-site-data) .

Gdy App Service konfiguracja zostanie zmieniona w Azure Portal, aktualizacje zazwyczaj zaczną obowiązywać szybko, ale nie będą natychmiastowo. Czasami przed ponownym uruchomieniem App Service należy zaczekać krótki okres, aby zmiany konfiguracji zaczęły obowiązywać.

W przypadku rozwiązywania problemów z ładowaniem certyfikatu wykonaj następujące polecenie w powłoce poleceń Azure Portal [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) programu PowerShell. Polecenie zawiera listę certyfikatów, do których aplikacja może uzyskać dostęp z `My`  >  `CurrentUser` magazynu certyfikatów. Dane wyjściowe obejmują tematy certyfikatów i odciski palców przydatne podczas debugowania aplikacji:

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wdrożenie do Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importowanie certyfikatu z Key Vault (dokumentacja platformy Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
