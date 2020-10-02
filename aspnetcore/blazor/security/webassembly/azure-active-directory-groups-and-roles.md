---
title: ASP.NET Core Blazor WebAssembly z grupami Azure Active Directory i rolami
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor WebAssembly program, aby używać grup i ról Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636780"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Grupy Azure Active Directory (AAD), role administratorów i role zdefiniowane przez użytkownika

Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :

* Grupy zdefiniowane przez użytkownika
  * Zabezpieczenia
  * Microsoft 365
  * Dystrybucja
* Role
  * Role administratora usługi AAD
  * Role zdefiniowane przez użytkownika

Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:

* [Autonomiczne z kontami Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Autonomiczne z usługą AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hostowane z usługą AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Uprawnienie Microsoft Graph interfejsu API

Wywołanie [interfejsu API Microsoft Graph](/graph/use-the-api) jest wymagane dla wszystkich użytkowników aplikacji mających więcej niż pięć ról administratora usługi AAD i członkostwa w grupach zabezpieczeń.

Aby zezwolić na interfejs API programu Graph wywołań, nadaj aplikacji autonomicznej lub klienckiej w hostowanym Blazor rozwiązaniu dowolne z następujących [uprawnień interfejs API programu Graph](/graph/permissions-reference) w Azure Portal:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` jest uprawnieniem z najniższymi uprawnieniami i jest uprawnieniem używanym do przykładu opisanego w tym artykule.

## <a name="user-defined-groups-and-administrator-roles"></a>Grupy zdefiniowane przez użytkownika i role administratorów

Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure. Przypisywanie użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i ról administratorów usługi AAD.

* [Role korzystające z grup zabezpieczeń usługi Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` przypisane](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi AAD.

Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON. Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .

Gdy liczba przypisanych ról administratora usługi AAD i grup zdefiniowanych przez użytkownika przekracza pięć, w usłudze AAD zostanie wysłane zgłoszenie do `hasgroups` `true` wartości zamiast wysyłania `groups` żądania. Wszystkie aplikacje, które mogą mieć więcej niż pięć ról i grup przypisanych do swoich użytkowników, muszą wykonać oddzielne wywołanie interfejs API programu Graph, aby uzyskać role i grupy użytkowników. Przykładowa implementacja podana w tym artykule dotyczy tego scenariusza. Aby uzyskać więcej informacji, zobacz `groups` `hasgroups` artykuł i informacje o oświadczeniach w [tokenach dostępu do platformy tożsamości firmy Microsoft: oświadczenie dotyczące ładunku](/azure/active-directory/develop/access-tokens#payload-claims) .

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról. Przypisz pustą tablicę do każdej właściwości, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach później.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

W aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego Blazor Utwórz klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> . Użyj poprawnego zakresu (uprawnienia) dla wywołań interfejs API programu Graph, które uzyskują informacje o rolach i grupach.

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

W programie `Program.Main` ( `Program.cs` ) Dodaj <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> usługę implementacji i Dodaj nazwę <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejs API programu Graph. Poniższy przykład nazywa klienta `GraphAPI` :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Utwórz klasy obiektów katalogu usługi AAD, aby otrzymywać role i grupy protokołu Open Data Protocol (OData) z wywołania interfejs API programu Graph. Dane OData docierają do formatu JSON i wywołanie do <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> wypełnienia wystąpienia `DirectoryObjects` klasy.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

Utwórz niestandardową fabrykę użytkowników, aby przetwarzać oświadczenia ról i grup. Poniższa przykładowa implementacja obsługuje również `roles` tablicę roszczeń, która znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) . Jeśli `hasgroups` jest obecny wniosek, nazwa <xref:System.Net.Http.HttpClient> jest używana do autoryzowania żądania interfejs API programu Graph, aby uzyskać role i grupy użytkowników. W tej implementacji jest stosowany Identity punkt końcowy platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([Dokumentacja interfejsu API](/graph/api/user-list-memberof)). Wskazówki zawarte w tym temacie zostaną zaktualizowane dla Identity wersji 2.0, gdy pakiety MSAL zostaną uaktualnione do wersji 2.0.

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Nie trzeba podawać kodu, aby usunąć pierwotne `groups` zastrzeżenie, jeśli jest obecny, ponieważ jest ono automatycznie usuwane przez platformę.

> [!NOTE]
> Podejście w tym przykładzie:
>
> * Dodaje klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , aby dołączyć tokeny dostępu do żądań wychodzących.
> * Dodaje nazwane <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejsu API sieci Web do bezpiecznego, zewnętrznego punktu końcowego interfejsu API sieci Web.
> * Używa nazwy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań.
>
> Ogólny zakres tego podejścia znajduje się w <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artykule.

Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) aplikacji autonomicznej lub aplikacji klienta hostowanego Blazor rozwiązania. Wyrażanie zgody na `Directory.Read.All` zakres uprawnień jako dodatkowy zakres dla aplikacji:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` . Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids) .

W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.

[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.

Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Autoryzuj dostęp do interfejsu API serwera dla grup zdefiniowanych przez użytkownika i ról administratorów

Oprócz autoryzowania użytkowników w aplikacji webassembly po stronie klienta w celu uzyskania dostępu do stron i zasobów, interfejs API serwera może autoryzować użytkowników w celu uzyskania dostępu do bezpiecznych punktów końcowych interfejsu API. Gdy aplikacja *serwera* zweryfikuje token dostępu użytkownika:

* Aplikacja używa niezmiennego [identyfikatora obiektu użytkownika ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) z tokenu JWT ( `id_token` ) w celu uzyskania tokenu dostępu dla interfejs API programu Graph.
* Wywołanie interfejs API programu Graph umożliwia uzyskanie członkostwa w roli administratora i grupy zabezpieczeń zdefiniowane przez użytkownika platformy Azure.
* Członkostwa są używane do ustanawiania `group` oświadczeń.
* [Zasady autoryzacji](xref:security/authorization/policies) mogą służyć do ograniczania dostępu użytkowników do punktów końcowych interfejsu API serwera.

> [!NOTE]
> Te wskazówki nie obejmują obecnie autoryzacji użytkowników na podstawie [ról zdefiniowanych przez użytkownika usługi AAD](#user-defined-roles).

### <a name="packages"></a>Pakiety

Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:

* [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Firma Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="azure-configuration"></a>Konfiguracja platformy Azure

* Upewnij się, że rejestracja aplikacji *serwera* ma dostęp do interfejsu API interfejs API programu Graph uprawnienia dla programu `Directory.Read.All` , który jest poziomem dostępu o najniższych uprawnieniach dla grup zabezpieczeń. Upewnij się, że zgoda administratora została zastosowana do uprawnienia po wprowadzeniu przypisania uprawnień.
* Przypisz nowy wpis tajny klienta do aplikacji *serwera* . Zanotuj wpis tajny konfiguracji aplikacji w sekcji [Ustawienia aplikacji](#app-settings) .

### <a name="app-settings"></a>Ustawienia aplikacji

W pliku ustawień aplikacji ( `appsettings.json` lub `appsettings.Production.json` ) Utwórz `ClientSecret` wpis z użyciem klucza tajnego klienta aplikacji *serwera* z Azure Portal:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Na przykład:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a>Zasady autoryzacji

Utwórz [zasady autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń usługi AAD i ról administratora usługi AAD *Server* w aplikacji serwera `Startup.ConfigureServices` ( `Startup.cs` ) na podstawie identyfikatorów obiektów grup i [identyfikatorów obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids).

Na przykład zasady roli administrator rozliczeń platformy Azure mają następującą konfigurację:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.

### <a name="controller-access"></a>Dostęp do kontrolera

Wymagaj zasad na kontrolerach aplikacji *serwera* .

Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdmin` , zgodnie z konfiguracją w sekcji [zasady autoryzacji](#authorization-policies) :

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a>Konfiguracja usługi

W metodzie aplikacji *serwera* `Startup.ConfigureServices` Dodaj logikę, aby interfejs API programu Graph wywołać i ustanowić oświadczenia użytkowników `group` dla grup zabezpieczeń i ról użytkownika.

> [!NOTE]
> Przykładowy kod w tej sekcji używa Active Directory Authentication Library (ADAL), który jest oparty na Identity platformie Microsoft Platform v 1.0. Ten temat zostanie zaktualizowany w Identity wersji 2.0 dla programu .NET 5. Śledź postęp tej pracy przez monitorowanie [[RC1] Microsoft Identity platform 2,0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).

Dodatkowe przestrzenie nazw są wymagane dla kodu w `Startup` klasie aplikacji *serwera* . Poniższy zestaw `using` instrukcji zawiera przestrzenie nazw wymagane dla kodu, który następuje po tej sekcji:

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.
* W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu. Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.

W pliku `Startup.ConfigureServices`:

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

W powyższym przykładzie:

* Najpierw podjęto próbę pozyskania tokenu dyskretnego, <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ponieważ token dostępu mógł już zostać zapisany w pamięci podręcznej tokenów biblioteki ADAL. Pobieranie tokenu z pamięci podręcznej jest szybsze niż żądanie nowego tokenu.
* Jeśli token dostępu nie zostanie uzyskany z pamięci podręcznej ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> lub <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> jest zgłaszany), potwierdzenie użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) zostanie wykonane przy użyciu poświadczeń klienta ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) w celu uzyskania tokenu w imieniu użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ). Następnie można dalej `Microsoft.Graph.GraphServiceClient` używać tokenu, aby wywołać interfejs API programu Graph. Token jest umieszczany w pamięci podręcznej tokenów ADAL. W przypadku przyszłych interfejs API programu Graph wywołań dla tego samego użytkownika token jest uzyskiwany z pamięci podręcznej dyskretnie z <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .

Kod w <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie uzyskuje członkostw przechodnich. Aby zmienić kod w celu uzyskania bezpośrednich i przechodnich członkostw:

* Dla wiersza kodu:

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Zamień poprzedni wiersz na:

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Dla wiersza kodu:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Zamień poprzedni wiersz na:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

Kod w programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie rozróżnia grup zabezpieczeń usługi AAD i ról administratorów usługi AAD podczas tworzenia oświadczeń. Aby można było rozróżnić grupę i role w aplikacji, należy sprawdzić `entry.ODataType` podczas iterowania przez grupy i role. Aby utworzyć oddzielną grupę zabezpieczeń i oświadczenia ról, należy użyć kodu podobnego do poniższego:

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a>Role zdefiniowane przez użytkownika

Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.

Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.

W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:

* `admin`
* `developer`

> [!NOTE]
> Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure. Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.
>
> W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.

Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON. Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.

`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON. Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego Blazor , jak pokazano w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) . Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.

W aplikacji `Program.Main` autonomicznej lub aplikacji klienckiej rozwiązania hostowanego Blazor należy określić wartość " `role` " jako rolę żądania:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Metody autoryzacji składników są w tym momencie funkcjonalne. Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:

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

## <a name="aad-administrator-role-object-ids"></a>Identyfikatory obiektów roli administratora usługi AAD

Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń. Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i role administratorów usługi AAD](#user-defined-groups-and-administrator-roles) .

Rola administratora usługi AAD | Identyfikator obiektu
--- | ---
Administrator aplikacji | fa11557b-4f15-4ddd-85d5-313c7cd74047
Deweloper aplikacji | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrator uwierzytelniania | 02d110a1-96b1-419e-af87-746461b60ed7
Administrator usługi Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection administrator | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrator zestawu kluczy B2C IEF | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrator zasad B2C IEF | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrator przepływu użytkownika B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C atrybutu przepływu użytkownika | dd0baca0-a535-48c1-b871-8431abe16452
Administrator rozliczeń | 69ff516a-b57d-4697-a429-9de4af7b5609
Administrator aplikacji w chmurze | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrator urządzenia w chmurze | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrator zgodności | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrator danych zgodności | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrator dostępu warunkowego | 8f71a611-137d-49af-87ad-e97f1fd5da76
Osoba zatwierdzająca dostęp do skrytki klienta | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Administrator usługi Desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Czytelnicy katalogów | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrator systemu Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administrator programu Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAdministrator dostawcy zewnętrznego | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrator globalny | a45ba61b-44db-462c-924b-3b2719152588
Czytnik globalny | f6903b21-6aba-4124-B44c-76671796b9d5
Administrator grup | 158b3e5a-d89d-460b-92b5-3b34985f0197
Zapraszający gościa | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrator pomocy technicznej | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrator usługi Intune | 79950741-23fa-4189-b2cb-46640601c497
Usługi kaizala administrator | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrator licencji | 3355458a-e423-44bf-8b98-4ac5e572cea5
Czytnik prywatności centrum wiadomości | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Czytelnik centrum wiadomości | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrator aplikacji pakietu Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrator haseł | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI administrator | 984e83b8-8337-4255-91a1-acb663175ab4
platforma Power administrator | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrator uprzywilejowanego uwierzytelniania | 0829f731-b46d-419f-9742-aeb122367d11
Administrator ról uprzywilejowanych | f20a725a-d1c8-4107-83ea-1171c97d00c7
Czytelnik raportów | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrator wyszukiwania | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Edytor wyszukiwania | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrator zabezpieczeń | 20fa50e3-6531-44d8-bd39-b251420568ad
Operator zabezpieczeń | 43aae017-8e51-4188-91ab-e6debd572800
Czytelnik zabezpieczeń | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrator pomocy technicznej usługi | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrator programu SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrator programu Skype dla firm | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administratorzy zespołu ds. komunikacji | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Inżynierowie pomocy technicznej dla zespołów | 802dd94e-d717-46f6-af98-b9167071e9fc
Zespoły komunikacyjne specjalisty | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrator usługi Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrator użytkowników | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
