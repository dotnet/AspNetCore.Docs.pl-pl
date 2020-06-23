---
title: ASP.NET Core Blazor zestawu webassembly przy użyciu Azure Active Directory grup i ról
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor zestaw webassembly do korzystania z Azure Active Directory grup i ról.
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ed49ba13842f2b5805250d8c12535397c542cfd4
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242878"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Grupy usługi Azure AD, role administracyjne i role zdefiniowane przez użytkownika

Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :

* Grupy zdefiniowane przez użytkownika
  * Zabezpieczenia
  * O365
  * Dystrybucja
* Role
  * Wbudowane role administracyjne
  * Role zdefiniowane przez użytkownika

Wskazówki zawarte w tym artykule dotyczą Blazor scenariuszy wdrażania usługi webassembly w usłudze AAD, które opisano w następujących tematach:

* [Autonomiczne z kontami Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Autonomiczne z usługą AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hostowane z usługą AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Grupy zdefiniowane przez użytkownika i wbudowane role administracyjne

Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure. Przypisz użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i wbudowanych ról administracyjnych.

* [Role korzystające z grup zabezpieczeń usługi Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims`przypisane](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* w usłudze AAD.

Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON. Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

Utwórz niestandardową fabrykę użytkowników w aplikacji autonomicznej lub aplikacji klienckiej hostowanego rozwiązania. Poniższa fabryka jest również skonfigurowana do obsługi `roles` tablic roszczeń, które są omówione w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
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

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `groups` żądania, ponieważ jest ono automatycznie usuwane przez platformę.

Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` . Poniższy przykład tworzy zasady dla wbudowanej roli *administratora rozliczeń* w usłudze AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory grup ról usługi AAD administracyjnych](#aad-adminstrative-role-group-ids) .

W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.

[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
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

### <a name="user-defined-roles"></a>Role zdefiniowane przez użytkownika

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

`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON. Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego, jak pokazano w sekcji [zdefiniowane przez użytkownika grupy i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) . Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.

W aplikacji `Program.Main` autonomicznej lub aplikacji klienckiej rozwiązania hostowanego należy określić wartość " `role` " jako rolę żądania:

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

## <a name="aad-adminstrative-role-group-ids"></a>Identyfikatory grup ról administracyjnych usługi AAD

Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń. Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) .

Rola administracyjna usługi AAD | Identyfikator obiektu
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

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
