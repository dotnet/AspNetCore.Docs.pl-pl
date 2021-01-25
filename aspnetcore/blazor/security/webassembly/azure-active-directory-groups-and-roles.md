---
title: ASP.NET Core Blazor WebAssembly z grupami Azure Active Directory i rolami
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor WebAssembly program, aby używać grup i ról Azure Active Directory.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 8d18a8f9282e83c3b3ef5b9c7c6651c9b525a21f
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751583"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Grupy Azure Active Directory (AAD), role administratorów i role aplikacji

Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> Ten artykuł ma zastosowanie do Blazor ASP.NET Core aplikacji w wersji 3,1 z firmą Microsoft Identity 1,0 i wkrótce zostanie zaktualizowany do 5,0 z Identity 2,0. Aby uzyskać więcej informacji, zobacz następujący problem z usługą GitHub i żądanie ściągnięcia. Karta **zmiany plików** w żądaniu ściągnięcia zawiera wersję roboczą tekstu i przykłady aktualizacji artykułu. Po przejrzeniu i ostatecznych aktualizacjach żądanie ściągnięcia zostanie scalone z zestawem dokumentacji na żywo.
>
> * Problem: [ Blazor WASM z grupami i rolami usługi AAD (#17683 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)
> * Żądanie ściągnięcia: [ Blazor temat grupy i role usługi AAD 5,0 (#20856 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)

Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :

* Grupy
  * Zabezpieczenia
  * Microsoft 365
  * Dystrybucja
* Role
  * Role administratora usługi AAD
  * Role aplikacji

Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:

* [Autonomiczne z kontami Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Autonomiczne z usługą AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hostowane z usługą AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

Wskazówki zawarte w artykule zawierają instrukcje dotyczące aplikacji klienta i serwera:

* **Klient**: Aplikacje autonomiczne Blazor WebAssembly lub *`Client`* aplikacja hostowanego Blazor rozwiązania.
* **Serwer**: autonomiczny interfejs api serwera ASP.NET Core/aplikacje Web API lub *`Server`* aplikacja hostowanego Blazor rozwiązania.

## <a name="scopes"></a>Zakresy

Aby zezwolić na [Microsoft Graph wywołań interfejsu API](/graph/use-the-api) dla profilu użytkownika, przypisania roli i danych członkostwa w grupie, **Klient** programu jest skonfigurowany przy użyciu ( `https://graph.microsoft.com/User.Read` ) [interfejs API programu Graph uprawnień (zakres)](/graph/permissions-reference) w Azure Portal.

Aplikacja **serwera** , która wywołuje interfejs API programu Graph dla danych członkostwa roli i grupy `GroupMember.Read.All` () ( `https://graph.microsoft.com/GroupMember.Read.All` ) [interfejs API programu Graph uprawnienia (zakres)](/graph/permissions-reference) w Azure Portal.

Te zakresy są wymagane oprócz zakresów wymaganych w scenariuszach wdrażania usługi AAD opisanych w temacie w pierwszej sekcji tego artykułu.

> [!NOTE]
> Słowa "uprawnienie" i "zakres" są używane zamiennie w Azure Portal i w różnych zestawach dokumentacji firmy Microsoft i zewnętrznych. W tym artykule użyto słowa "Scope" w ramach uprawnień przypisanych do aplikacji w Azure Portal.

## <a name="group-membership-claims-attribute"></a>Atrybut oświadczeń członkostwa w grupie

W manifeście aplikacji w Azure Portal dla aplikacji **klienta** i **serwera** Ustaw [ `groupMembershipClaims` atrybut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) na `All` . Wartość `All` powoduje uzyskanie wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról, do których należy użytkownik zalogowany.

1. Otwórz Azure Portal rejestracji aplikacji.
1. Wybierz pozycję **Zarządzaj**  >  **manifest** na pasku bocznym.
1. Znajdź `groupMembershipClaims` atrybut.
1. Ustaw wartość na `All`.
1. Wybierz ikonę **Zapisz**.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Niestandardowe konto użytkownika

Przypisz użytkowników do grup zabezpieczeń usługi AAD i ról administratorów usługi AAD w Azure Portal.

Przykłady w tym artykule:

* Załóżmy, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi aad w Azure Portal dzierżawy usługi AAD na potrzeby autoryzacji dostępu do danych interfejsu API serwera.
* Użyj [zasad autoryzacji](xref:security/authorization/policies) , aby kontrolować dostęp w aplikacjach **klienta** i **serwera** .

W aplikacji **klienckiej** rozwiń, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Aby uwzględnić właściwości dla:

* `Roles`: Tablica ról aplikacji usługi AAD (pokryte w sekcji [role aplikacji](#app-roles) )
* `Wids`: Role administratora usługi AAD w [oświadczeniach dobrze znanych identyfikatorów ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`: Niezmiennego [żądania identyfikatora obiektu ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (unikatowa identyfikacja użytkownika w ramach dzierżaw i między dzierżawcami)

Przypisz pustą tablicę do każdej właściwości tablicy, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach.

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

Dodaj odwołanie do pakietu do pliku projektu aplikacji **klienta** dla programu [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Dodaj klasy i konfigurację narzędzia zestawu Graph SDK w sekcji *zestaw Graph SDK* <xref:blazor/security/webassembly/graph-api#graph-sdk> artykułu. W `GraphClientExtensions` klasie Określ `User.Read` zakres dla tokenu dostępu w `AuthenticateRequestAsync` metodzie:

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Dodaj następującą niestandardową fabrykę kont użytkowników do aplikacji **klienckiej** . Niestandardowa fabryka użytkowników służy do ustanawiania:

* Oświadczenia ról aplikacji ( `appRole` ) (omówione w sekcji [role aplikacji](#app-roles) )
* Oświadczenia roli administratora usługi AAD ( `directoryRole` )
* Przykładowe zdarzenie dotyczące danych profilu użytkownika dla numeru telefonu komórkowego użytkownika ( `mobilePhone` )
* Oświadczenia grupy usługi AAD ( `directoryGroup` )

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
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
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Poprzedzający kod nie obejmuje członkostw przechodnich. Jeśli aplikacja wymaga bezpośrednich i przechodnich oświadczeń członkostwa w grupie, Zamień `MemberOf` Właściwość ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) na `TransitiveMemberOf` () `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` .

Powyższy kod ignoruje oświadczenia członkostwa w grupie ( `groups` ), które są rolami administratora usługi AAD ( `#microsoft.graph.directoryRole` Typ), ponieważ wartości identyfikatorów GUID zwracane przez Identity platformę Microsoft 2,0 to **identyfikatory jednostek** roli administratora usługi AAD, a nie [**identyfikatory szablonu roli**](/azure/active-directory/roles/permissions-reference#role-template-ids). Identyfikatory jednostek nie są stabilne wśród dzierżawców na Identity platformie Microsoft Platform 2,0 i nie powinny być używane do tworzenia zasad autoryzacji dla użytkowników w aplikacjach. Zawsze używaj **identyfikatorów szablonów ról** dla ról administratorów usługi AAD **dostarczonych przez `wids` oświadczenia**.

W `Program.Main` aplikacji **klienckiej** Skonfiguruj uwierzytelnianie MSAL, tak aby używało fabryki kont użytkowników niestandardowych.

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>Konfiguracja autoryzacji

W aplikacji **klienckiej** Utwórz [zasady](xref:security/authorization/policies) dla każdej [roli aplikacji](#app-roles), roli administratora usługi AAD lub grupy zabezpieczeń w temacie `Program.Main` . Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Aby uzyskać pełną listę identyfikatorów ról administratorów usługi AAD, zobacz [identyfikatory szablonów ról](/azure/active-directory/roles/permissions-reference#role-template-ids) w dokumentacji platformy Azure. Aby uzyskać więcej informacji na temat zasad autoryzacji, zobacz <xref:security/authorization/policies> .

W poniższych przykładach aplikacja **kliencka** używa powyższych zasad do autoryzowania użytkownika.

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

Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

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

## <a name="authorize-server-apiweb-api-access"></a>Autoryzuj dostęp do interfejsu API serwera/dostępu do interfejsu API sieci Web

Aplikacja interfejsu API **serwera** może autoryzować użytkowników do uzyskiwania dostępu do bezpiecznych punktów końcowych interfejsu API przy użyciu [zasad autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń, ról administratorów usługi AAD i ról aplikacji, gdy token dostępu zawiera `groups` , `wids` i `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` oświadczenia. Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD w programie `Startup.ConfigureServices` przy użyciu `wids` oświadczeń (dobrze znanych identyfikatorów/identyfikatorów szablonów ról):

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Aby uzyskać pełną listę identyfikatorów ról administratorów usługi AAD, zobacz [identyfikatory szablonów ról](/azure/active-directory/roles/permissions-reference#role-template-ids) w dokumentacji platformy Azure. Aby uzyskać więcej informacji na temat zasad autoryzacji, zobacz <xref:security/authorization/policies> .

Dostęp do kontrolera w aplikacji **serwerowej** może opierać się na użyciu [ `[Authorize]` atrybutu](xref:security/authorization/simple) o nazwie zasad (Dokumentacja interfejsu API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).

Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdministrator` :

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.

## <a name="app-roles"></a>Role aplikacji

Aby skonfigurować aplikację w Azure Portal w celu zapewnienia oświadczeń członkostwa ról aplikacji, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.

W poniższym przykładzie przyjęto założenie, że aplikacje **klienta** i **serwera** są skonfigurowane z dwiema rolami, a role są przypisane do użytkownika testowego:

* `admin`
* `developer`

> [!NOTE]
> Podczas tworzenia aplikacji hostowanej Blazor WebAssembly lub pary klient-serwer aplikacji autonomicznych (autonomiczna Blazor WebAssembly aplikacja i autonomiczna aplikacja interfejsu api serwera ASP.NET Core/aplikacji interfejsu API sieci Web), `appRoles` Właściwość manifestu zarówno klienta, jak i serwera Azure Portal rejestracji aplikacji musi zawierać te same skonfigurowane role. Po ustanowieniu ról w manifeście aplikacji klienta skopiuj je w całości do manifestu aplikacji serwera. Jeśli nie zdublowano manifestu `appRoles` między rejestracjami aplikacji klienta i serwera, oświadczenia ról nie są ustanawiane dla uwierzytelnionych użytkowników interfejsu API serwera/interfejsu API sieci Web, nawet jeśli ich token dostępu ma poprawne oświadczenia ról.

> [!NOTE]
> Chociaż nie można przypisywać ról do grup bez konta Azure AD — wersja Premium, można przypisywać role do użytkowników i odbierać role dla użytkowników przy użyciu standardowego konta platformy Azure. Wskazówki zawarte w tej sekcji nie wymagają konta usługi AAD Premium.
>
> W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.

`CustomAccountFactory`Pokazane w sekcji [niestandardowe konto użytkownika](#custom-user-account) jest skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON. Dodaj i zarejestruj `CustomAccountFactory` w aplikacji **klienckiej** , jak pokazano w sekcji [niestandardowe konto użytkownika](#custom-user-account) . Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.

W `Program.Main` aplikacji **klienckiej** Określ wartość " `appRole` " jako rolę roszczeń dla <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> kontroli:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> Jeśli wolisz używać `directoryRoles` roszczeń (Dodaj role administratorów), Przypisz " `directoryRoles` " do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .

W `Startup.ConfigureServices` aplikacji **serwerowej** Określ wartość " `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` " jako rolę roszczeń dla <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> kontroli:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> Jeśli wolisz używać `wids` roszczeń (Dodaj role administratorów), Przypisz " `wids` " do <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .

Metody autoryzacji składników są w tym momencie funkcjonalne. Każdy mechanizm autoryzacji w składnikach aplikacji **klienckiej** może używać `admin` roli do autoryzowania użytkownika:

* [`AuthorizeView` składnika](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Logika proceduralna](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Obsługiwane są wiele testów ról:

* Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `AuthorizeView` składnikiem:

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` ze `AuthorizeView` składnikiem:

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `[Authorize]` atrybutem:

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z `[Authorize]` atrybutem:

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z kodem proceduralnym:

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z kodem proceduralnym poprzez zmianę [warunku or ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) na [Conditional i ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) w poprzednim przykładzie:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

Każdy mechanizm autoryzacji w kontrolerach aplikacji **serwera** może używać `admin` roli do autoryzowania użytkownika:

* [ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Logika proceduralna](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Obsługiwane są wiele testów ról:

* Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `[Authorize]` atrybutem:

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z `[Authorize]` atrybutem:

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z kodem proceduralnym:

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z kodem proceduralnym poprzez zmianę [warunku or ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) na [Conditional i ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) w poprzednim przykładzie:

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Identyfikatory szablonów ról (dokumentacja platformy Azure)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` Attribute (dokumentacja platformy Azure)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [Instrukcje: Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie (dokumentacja platformy Azure)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Role aplikacji (dokumentacja platformy Azure)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
