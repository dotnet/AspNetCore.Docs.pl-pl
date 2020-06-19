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
ms.openlocfilehash: 99ebe43da191153aa98cce6bae8fe98035bc7d6f
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103986"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="656bf-103">Grupy usługi Azure AD, role administracyjne i role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="656bf-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="656bf-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="656bf-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="656bf-105">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="656bf-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="656bf-106">Grupy zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="656bf-106">User-defined groups</span></span>
  * <span data-ttu-id="656bf-107">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="656bf-107">Security</span></span>
  * <span data-ttu-id="656bf-108">O365</span><span class="sxs-lookup"><span data-stu-id="656bf-108">O365</span></span>
  * <span data-ttu-id="656bf-109">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="656bf-109">Distribution</span></span>
* <span data-ttu-id="656bf-110">Role</span><span class="sxs-lookup"><span data-stu-id="656bf-110">Roles</span></span>
  * <span data-ttu-id="656bf-111">Wbudowane role administracyjne</span><span class="sxs-lookup"><span data-stu-id="656bf-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="656bf-112">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="656bf-112">User-defined roles</span></span>

<span data-ttu-id="656bf-113">Wskazówki zawarte w tym artykule dotyczą Blazor scenariuszy wdrażania usługi webassembly w usłudze AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="656bf-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="656bf-114">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="656bf-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="656bf-115">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="656bf-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="656bf-116">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="656bf-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="656bf-117">Grupy zdefiniowane przez użytkownika i wbudowane role administracyjne</span><span class="sxs-lookup"><span data-stu-id="656bf-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="656bf-118">Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="656bf-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="656bf-119">Przypisz użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i wbudowanych ról administracyjnych.</span><span class="sxs-lookup"><span data-stu-id="656bf-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="656bf-120">Role korzystające z grup zabezpieczeń usługi Azure AD</span><span class="sxs-lookup"><span data-stu-id="656bf-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="656bf-121">groupMembershipClaims — atrybut</span><span class="sxs-lookup"><span data-stu-id="656bf-121">groupMembershipClaims attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="656bf-122">W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="656bf-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="656bf-123">Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="656bf-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="656bf-124">Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="656bf-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="656bf-125"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.</span><span class="sxs-lookup"><span data-stu-id="656bf-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="656bf-126">*CustomUserAccount.cs*:</span><span class="sxs-lookup"><span data-stu-id="656bf-126">*CustomUserAccount.cs*:</span></span>

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

<span data-ttu-id="656bf-127">Utwórz niestandardową fabrykę użytkowników w aplikacji autonomicznej lub aplikacji klienckiej hostowanego rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="656bf-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="656bf-128">Poniższa fabryka jest również skonfigurowana do obsługi `roles` tablic roszczeń, które są omówione w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) :</span><span class="sxs-lookup"><span data-stu-id="656bf-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

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

<span data-ttu-id="656bf-129">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `groups` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="656bf-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="656bf-130">Zarejestruj fabrykę w `Program.Main` (*program.cs*) aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego:</span><span class="sxs-lookup"><span data-stu-id="656bf-130">Register the factory in `Program.Main` (*Program.cs*) of the standalone app or Client app of a Hosted solution:</span></span>

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

<span data-ttu-id="656bf-131">Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="656bf-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="656bf-132">Poniższy przykład tworzy zasady dla wbudowanej roli *administratora rozliczeń* w usłudze AAD:</span><span class="sxs-lookup"><span data-stu-id="656bf-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="656bf-133">Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory grup ról usługi AAD administracyjnych](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="656bf-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="656bf-134">W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="656bf-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="656bf-135">[Składnik AuthorizeView](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="656bf-135">The [AuthorizeView component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="656bf-136">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="656bf-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="656bf-137">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="656bf-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="656bf-138">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="656bf-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="656bf-139">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="656bf-139">User-defined roles</span></span>

<span data-ttu-id="656bf-140">Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="656bf-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="656bf-141">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="656bf-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="656bf-142">W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:</span><span class="sxs-lookup"><span data-stu-id="656bf-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="656bf-143">Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="656bf-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="656bf-144">Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.</span><span class="sxs-lookup"><span data-stu-id="656bf-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="656bf-145">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="656bf-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="656bf-146">Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="656bf-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="656bf-147">Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="656bf-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="656bf-148">`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="656bf-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="656bf-149">Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego, jak pokazano w sekcji [zdefiniowane przez użytkownika grupy i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="656bf-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="656bf-150">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="656bf-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="656bf-151">W aplikacji `Program.Main` autonomicznej lub aplikacji klienckiej rozwiązania hostowanego należy określić wartość " `role` " jako rolę żądania:</span><span class="sxs-lookup"><span data-stu-id="656bf-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="656bf-152">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="656bf-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="656bf-153">Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="656bf-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="656bf-154">[AuthorizeView — składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="656bf-154">[AuthorizeView component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="656bf-155">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="656bf-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="656bf-156">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="656bf-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="656bf-157">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="656bf-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="656bf-158">Identyfikatory grup ról administracyjnych usługi AAD</span><span class="sxs-lookup"><span data-stu-id="656bf-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="656bf-159">Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="656bf-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="656bf-160">Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="656bf-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="656bf-161">Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="656bf-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="656bf-162">Rola administracyjna usługi AAD</span><span class="sxs-lookup"><span data-stu-id="656bf-162">AAD Administrative Role</span></span> | <span data-ttu-id="656bf-163">Identyfikator obiektu</span><span class="sxs-lookup"><span data-stu-id="656bf-163">Object ID</span></span>
--- | ---
<span data-ttu-id="656bf-164">Administrator aplikacji</span><span class="sxs-lookup"><span data-stu-id="656bf-164">Application administrator</span></span> | <span data-ttu-id="656bf-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="656bf-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="656bf-166">Deweloper aplikacji</span><span class="sxs-lookup"><span data-stu-id="656bf-166">Application developer</span></span> | <span data-ttu-id="656bf-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="656bf-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="656bf-168">Administrator uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="656bf-168">Authentication administrator</span></span> | <span data-ttu-id="656bf-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="656bf-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="656bf-170">Administrator usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="656bf-170">Azure DevOps administrator</span></span> | <span data-ttu-id="656bf-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="656bf-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="656bf-172">Azure Information Protection administrator</span><span class="sxs-lookup"><span data-stu-id="656bf-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="656bf-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="656bf-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="656bf-174">Administrator zestawu kluczy B2C IEF</span><span class="sxs-lookup"><span data-stu-id="656bf-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="656bf-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="656bf-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="656bf-176">Administrator zasad B2C IEF</span><span class="sxs-lookup"><span data-stu-id="656bf-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="656bf-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="656bf-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="656bf-178">Administrator przepływu użytkownika B2C</span><span class="sxs-lookup"><span data-stu-id="656bf-178">B2C user flow administrator</span></span> | <span data-ttu-id="656bf-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="656bf-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="656bf-180">B2C atrybutu przepływu użytkownika</span><span class="sxs-lookup"><span data-stu-id="656bf-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="656bf-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="656bf-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="656bf-182">Administrator rozliczeń</span><span class="sxs-lookup"><span data-stu-id="656bf-182">Billing administrator</span></span> | <span data-ttu-id="656bf-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="656bf-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="656bf-184">Administrator aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="656bf-184">Cloud application administrator</span></span> | <span data-ttu-id="656bf-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="656bf-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="656bf-186">Administrator urządzenia w chmurze</span><span class="sxs-lookup"><span data-stu-id="656bf-186">Cloud device administrator</span></span> | <span data-ttu-id="656bf-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="656bf-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="656bf-188">Administrator zgodności</span><span class="sxs-lookup"><span data-stu-id="656bf-188">Compliance administrator</span></span> | <span data-ttu-id="656bf-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="656bf-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="656bf-190">Administrator danych zgodności</span><span class="sxs-lookup"><span data-stu-id="656bf-190">Compliance data administrator</span></span> | <span data-ttu-id="656bf-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="656bf-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="656bf-192">Administrator dostępu warunkowego</span><span class="sxs-lookup"><span data-stu-id="656bf-192">Conditional Access administrator</span></span> | <span data-ttu-id="656bf-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="656bf-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="656bf-194">Osoba zatwierdzająca dostęp do skrytki klienta</span><span class="sxs-lookup"><span data-stu-id="656bf-194">Customer LockBox access approver</span></span> | <span data-ttu-id="656bf-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="656bf-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="656bf-196">Administrator usługi Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="656bf-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="656bf-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="656bf-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="656bf-198">Czytelnicy katalogów</span><span class="sxs-lookup"><span data-stu-id="656bf-198">Directory readers</span></span> | <span data-ttu-id="656bf-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="656bf-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="656bf-200">Administrator systemu Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="656bf-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="656bf-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="656bf-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="656bf-202">Administrator programu Exchange</span><span class="sxs-lookup"><span data-stu-id="656bf-202">Exchange administrator</span></span> | <span data-ttu-id="656bf-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="656bf-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="656bf-204">IdentityAdministrator dostawcy zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="656bf-204">External Identity Provider administrator</span></span> | <span data-ttu-id="656bf-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="656bf-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="656bf-206">Administrator globalny</span><span class="sxs-lookup"><span data-stu-id="656bf-206">Global administrator</span></span> | <span data-ttu-id="656bf-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="656bf-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="656bf-208">Czytnik globalny</span><span class="sxs-lookup"><span data-stu-id="656bf-208">Global reader</span></span> | <span data-ttu-id="656bf-209">f6903b21-6aba-4124-B44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="656bf-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="656bf-210">Administrator grup</span><span class="sxs-lookup"><span data-stu-id="656bf-210">Groups administrator</span></span> | <span data-ttu-id="656bf-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="656bf-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="656bf-212">Zapraszający gościa</span><span class="sxs-lookup"><span data-stu-id="656bf-212">Guest inviter</span></span> | <span data-ttu-id="656bf-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="656bf-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="656bf-214">Administrator pomocy technicznej</span><span class="sxs-lookup"><span data-stu-id="656bf-214">Helpdesk administrator</span></span> | <span data-ttu-id="656bf-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="656bf-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="656bf-216">Administrator usługi Intune</span><span class="sxs-lookup"><span data-stu-id="656bf-216">Intune administrator</span></span> | <span data-ttu-id="656bf-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="656bf-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="656bf-218">Usługi kaizala administrator</span><span class="sxs-lookup"><span data-stu-id="656bf-218">Kaizala administrator</span></span> | <span data-ttu-id="656bf-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="656bf-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="656bf-220">Administrator licencji</span><span class="sxs-lookup"><span data-stu-id="656bf-220">License administrator</span></span> | <span data-ttu-id="656bf-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="656bf-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="656bf-222">Czytnik prywatności centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="656bf-222">Message center privacy reader</span></span> | <span data-ttu-id="656bf-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="656bf-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="656bf-224">Czytelnik centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="656bf-224">Message center reader</span></span> | <span data-ttu-id="656bf-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="656bf-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="656bf-226">Administrator aplikacji pakietu Office</span><span class="sxs-lookup"><span data-stu-id="656bf-226">Office apps administrator</span></span> | <span data-ttu-id="656bf-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="656bf-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="656bf-228">Administrator haseł</span><span class="sxs-lookup"><span data-stu-id="656bf-228">Password administrator</span></span> | <span data-ttu-id="656bf-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="656bf-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="656bf-230">Power BI administrator</span><span class="sxs-lookup"><span data-stu-id="656bf-230">Power BI administrator</span></span> | <span data-ttu-id="656bf-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="656bf-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="656bf-232">platforma Power administrator</span><span class="sxs-lookup"><span data-stu-id="656bf-232">Power platform administrator</span></span> | <span data-ttu-id="656bf-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="656bf-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="656bf-234">Administrator uprzywilejowanego uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="656bf-234">Privileged authentication administrator</span></span> | <span data-ttu-id="656bf-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="656bf-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="656bf-236">Administrator ról uprzywilejowanych</span><span class="sxs-lookup"><span data-stu-id="656bf-236">Privileged role administrator</span></span> | <span data-ttu-id="656bf-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="656bf-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="656bf-238">Czytelnik raportów</span><span class="sxs-lookup"><span data-stu-id="656bf-238">Reports reader</span></span> | <span data-ttu-id="656bf-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="656bf-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="656bf-240">Administrator wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="656bf-240">Search administrator</span></span> | <span data-ttu-id="656bf-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="656bf-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="656bf-242">Edytor wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="656bf-242">Search editor</span></span> | <span data-ttu-id="656bf-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="656bf-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="656bf-244">Administrator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="656bf-244">Security administrator</span></span> | <span data-ttu-id="656bf-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="656bf-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="656bf-246">Operator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="656bf-246">Security operator</span></span> | <span data-ttu-id="656bf-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="656bf-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="656bf-248">Czytelnik zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="656bf-248">Security reader</span></span> | <span data-ttu-id="656bf-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="656bf-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="656bf-250">Administrator pomocy technicznej usługi</span><span class="sxs-lookup"><span data-stu-id="656bf-250">Service support administrator</span></span> | <span data-ttu-id="656bf-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="656bf-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="656bf-252">Administrator programu SharePoint</span><span class="sxs-lookup"><span data-stu-id="656bf-252">SharePoint administrator</span></span> | <span data-ttu-id="656bf-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="656bf-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="656bf-254">Administrator programu Skype dla firm</span><span class="sxs-lookup"><span data-stu-id="656bf-254">Skype for Business administrator</span></span> | <span data-ttu-id="656bf-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="656bf-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="656bf-256">Administratorzy zespołu ds. komunikacji</span><span class="sxs-lookup"><span data-stu-id="656bf-256">Teams Communications Administrator</span></span> | <span data-ttu-id="656bf-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="656bf-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="656bf-258">Inżynierowie pomocy technicznej dla zespołów</span><span class="sxs-lookup"><span data-stu-id="656bf-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="656bf-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="656bf-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="656bf-260">Zespoły komunikacyjne specjalisty</span><span class="sxs-lookup"><span data-stu-id="656bf-260">Teams Communications Specialist</span></span> | <span data-ttu-id="656bf-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="656bf-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="656bf-262">Administrator usługi Teams</span><span class="sxs-lookup"><span data-stu-id="656bf-262">Teams Service Administrator</span></span> | <span data-ttu-id="656bf-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="656bf-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="656bf-264">Administrator użytkowników</span><span class="sxs-lookup"><span data-stu-id="656bf-264">User administrator</span></span> | <span data-ttu-id="656bf-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="656bf-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="656bf-266">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="656bf-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
