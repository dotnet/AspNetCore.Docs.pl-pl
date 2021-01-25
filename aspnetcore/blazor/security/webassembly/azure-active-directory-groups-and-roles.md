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
ms.openlocfilehash: d1c75d85283b583d8bfd885fcd6552b69c2528c7
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758260"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="1810d-103">Grupy Azure Active Directory (AAD), role administratorów i role aplikacji</span><span class="sxs-lookup"><span data-stu-id="1810d-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="1810d-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1810d-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="1810d-105">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="1810d-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1810d-106">Grupy</span><span class="sxs-lookup"><span data-stu-id="1810d-106">Groups</span></span>
  * <span data-ttu-id="1810d-107">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="1810d-107">Security</span></span>
  * <span data-ttu-id="1810d-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="1810d-108">Microsoft 365</span></span>
  * <span data-ttu-id="1810d-109">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="1810d-109">Distribution</span></span>
* <span data-ttu-id="1810d-110">Role</span><span class="sxs-lookup"><span data-stu-id="1810d-110">Roles</span></span>
  * <span data-ttu-id="1810d-111">Role administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="1810d-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="1810d-112">Role aplikacji</span><span class="sxs-lookup"><span data-stu-id="1810d-112">App Roles</span></span>

<span data-ttu-id="1810d-113">Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="1810d-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="1810d-114">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="1810d-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="1810d-115">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="1810d-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="1810d-116">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="1810d-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="1810d-117">Wskazówki zawarte w artykule zawierają instrukcje dotyczące aplikacji klienta i serwera:</span><span class="sxs-lookup"><span data-stu-id="1810d-117">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="1810d-118">**Klient**: Aplikacje autonomiczne Blazor WebAssembly lub *`Client`* aplikacja hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1810d-118">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="1810d-119">**Serwer**: autonomiczny interfejs api serwera ASP.NET Core/aplikacje Web API lub *`Server`* aplikacja hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1810d-119">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="1810d-120">Zakresy</span><span class="sxs-lookup"><span data-stu-id="1810d-120">Scopes</span></span>

<span data-ttu-id="1810d-121">Aby zezwolić na [Microsoft Graph wywołań interfejsu API](/graph/use-the-api) dla profilu użytkownika, przypisania roli i danych członkostwa w grupie, **Klient** programu jest skonfigurowany przy użyciu ( `https://graph.microsoft.com/User.Read` ) [interfejs API programu Graph uprawnień (zakres)](/graph/permissions-reference) w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1810d-121">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="1810d-122">Aplikacja **serwera** , która wywołuje interfejs API programu Graph dla danych członkostwa roli i grupy `GroupMember.Read.All` () ( `https://graph.microsoft.com/GroupMember.Read.All` ) [interfejs API programu Graph uprawnienia (zakres)](/graph/permissions-reference) w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1810d-122">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="1810d-123">Te zakresy są wymagane oprócz zakresów wymaganych w scenariuszach wdrażania usługi AAD opisanych w temacie w pierwszej sekcji tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="1810d-123">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="1810d-124">Słowa "uprawnienie" i "zakres" są używane zamiennie w Azure Portal i w różnych zestawach dokumentacji firmy Microsoft i zewnętrznych.</span><span class="sxs-lookup"><span data-stu-id="1810d-124">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="1810d-125">W tym artykule użyto słowa "Scope" w ramach uprawnień przypisanych do aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1810d-125">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="1810d-126">Atrybut oświadczeń członkostwa w grupie</span><span class="sxs-lookup"><span data-stu-id="1810d-126">Group Membership Claims attribute</span></span>

<span data-ttu-id="1810d-127">W manifeście aplikacji w Azure Portal dla aplikacji **klienta** i **serwera** Ustaw [ `groupMembershipClaims` atrybut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) na `All` .</span><span class="sxs-lookup"><span data-stu-id="1810d-127">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="1810d-128">Wartość `All` powoduje uzyskanie wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról, do których należy użytkownik zalogowany.</span><span class="sxs-lookup"><span data-stu-id="1810d-128">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="1810d-129">Otwórz Azure Portal rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1810d-129">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="1810d-130">Wybierz pozycję **Zarządzaj**  >  **manifest** na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="1810d-130">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="1810d-131">Znajdź `groupMembershipClaims` atrybut.</span><span class="sxs-lookup"><span data-stu-id="1810d-131">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="1810d-132">Ustaw wartość na `All`.</span><span class="sxs-lookup"><span data-stu-id="1810d-132">Set the value to `All`.</span></span>
1. <span data-ttu-id="1810d-133">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1810d-133">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="1810d-134">Niestandardowe konto użytkownika</span><span class="sxs-lookup"><span data-stu-id="1810d-134">Custom user account</span></span>

<span data-ttu-id="1810d-135">Przypisz użytkowników do grup zabezpieczeń usługi AAD i ról administratorów usługi AAD w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1810d-135">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="1810d-136">Przykłady w tym artykule:</span><span class="sxs-lookup"><span data-stu-id="1810d-136">The examples in this article:</span></span>

* <span data-ttu-id="1810d-137">Załóżmy, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi aad w Azure Portal dzierżawy usługi AAD na potrzeby autoryzacji dostępu do danych interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="1810d-137">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="1810d-138">Użyj [zasad autoryzacji](xref:security/authorization/policies) , aby kontrolować dostęp w aplikacjach **klienta** i **serwera** .</span><span class="sxs-lookup"><span data-stu-id="1810d-138">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="1810d-139">W aplikacji **klienckiej** rozwiń, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Aby uwzględnić właściwości dla:</span><span class="sxs-lookup"><span data-stu-id="1810d-139">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="1810d-140">`Roles`: Tablica ról aplikacji usługi AAD (pokryte w sekcji [role aplikacji](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="1810d-140">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="1810d-141">`Wids`: Role administratora usługi AAD w [oświadczeniach dobrze znanych identyfikatorów ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="1810d-141">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="1810d-142">`Oid`: Niezmiennego [żądania identyfikatora obiektu ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (unikatowa identyfikacja użytkownika w ramach dzierżaw i między dzierżawcami)</span><span class="sxs-lookup"><span data-stu-id="1810d-142">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="1810d-143">Przypisz pustą tablicę do każdej właściwości tablicy, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach.</span><span class="sxs-lookup"><span data-stu-id="1810d-143">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="1810d-144">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="1810d-144">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="1810d-145">Dodaj odwołanie do pakietu do pliku projektu aplikacji **klienta** dla programu [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="1810d-145">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="1810d-146">Dodaj klasy i konfigurację narzędzia zestawu Graph SDK w sekcji *zestaw Graph SDK* <xref:blazor/security/webassembly/graph-api#graph-sdk> artykułu.</span><span class="sxs-lookup"><span data-stu-id="1810d-146">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="1810d-147">W `GraphClientExtensions` klasie Określ `User.Read` zakres dla tokenu dostępu w `AuthenticateRequestAsync` metodzie:</span><span class="sxs-lookup"><span data-stu-id="1810d-147">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="1810d-148">Dodaj następującą niestandardową fabrykę kont użytkowników do aplikacji **klienckiej** .</span><span class="sxs-lookup"><span data-stu-id="1810d-148">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="1810d-149">Niestandardowa fabryka użytkowników służy do ustanawiania:</span><span class="sxs-lookup"><span data-stu-id="1810d-149">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="1810d-150">Oświadczenia ról aplikacji ( `appRole` ) (omówione w sekcji [role aplikacji](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="1810d-150">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="1810d-151">Oświadczenia roli administratora usługi AAD ( `directoryRole` )</span><span class="sxs-lookup"><span data-stu-id="1810d-151">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="1810d-152">Przykładowe zdarzenie dotyczące danych profilu użytkownika dla numeru telefonu komórkowego użytkownika ( `mobilePhone` )</span><span class="sxs-lookup"><span data-stu-id="1810d-152">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="1810d-153">Oświadczenia grupy usługi AAD ( `directoryGroup` )</span><span class="sxs-lookup"><span data-stu-id="1810d-153">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="1810d-154">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="1810d-154">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="1810d-155">Poprzedzający kod nie obejmuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="1810d-155">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="1810d-156">Jeśli aplikacja wymaga bezpośrednich i przechodnich oświadczeń członkostwa w grupie, Zamień `MemberOf` Właściwość ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) na `TransitiveMemberOf` () `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1810d-156">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="1810d-157">Powyższy kod ignoruje oświadczenia członkostwa w grupie ( `groups` ), które są rolami administratora usługi AAD ( `#microsoft.graph.directoryRole` Typ), ponieważ wartości identyfikatorów GUID zwracane przez Identity platformę Microsoft 2,0 to **identyfikatory jednostek** roli administratora usługi AAD, a nie [**identyfikatory szablonu roli**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="1810d-157">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="1810d-158">Identyfikatory jednostek nie są stabilne wśród dzierżawców na Identity platformie Microsoft Platform 2,0 i nie powinny być używane do tworzenia zasad autoryzacji dla użytkowników w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="1810d-158">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="1810d-159">Zawsze używaj **identyfikatorów szablonów ról** dla ról administratorów usługi AAD **dostarczonych przez `wids` oświadczenia**.</span><span class="sxs-lookup"><span data-stu-id="1810d-159">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="1810d-160">W `Program.Main` aplikacji **klienckiej** Skonfiguruj uwierzytelnianie MSAL, tak aby używało fabryki kont użytkowników niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="1810d-160">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="1810d-161">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="1810d-161">`Program.cs`:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="1810d-162">Konfiguracja autoryzacji</span><span class="sxs-lookup"><span data-stu-id="1810d-162">Authorization configuration</span></span>

<span data-ttu-id="1810d-163">W aplikacji **klienckiej** Utwórz [zasady](xref:security/authorization/policies) dla każdej [roli aplikacji](#app-roles), roli administratora usługi AAD lub grupy zabezpieczeń w temacie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="1810d-163">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="1810d-164">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="1810d-164">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="1810d-165">Aby uzyskać pełną listę identyfikatorów ról administratorów usługi AAD, zobacz [identyfikatory szablonów ról](/azure/active-directory/roles/permissions-reference#role-template-ids) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1810d-165">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="1810d-166">Aby uzyskać więcej informacji na temat zasad autoryzacji, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="1810d-166">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="1810d-167">W poniższych przykładach aplikacja **kliencka** używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1810d-167">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="1810d-168">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="1810d-168">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="1810d-169">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="1810d-169">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="1810d-170">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="1810d-170">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="1810d-171">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="1810d-171">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="1810d-172">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="1810d-172">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="1810d-173">Autoryzuj dostęp do interfejsu API serwera/dostępu do interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="1810d-173">Authorize server API/web API access</span></span>

<span data-ttu-id="1810d-174">Aplikacja interfejsu API **serwera** może autoryzować użytkowników do uzyskiwania dostępu do bezpiecznych punktów końcowych interfejsu API przy użyciu [zasad autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń, ról administratorów usługi AAD i ról aplikacji, gdy token dostępu zawiera `groups` , `wids` i `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="1810d-174">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="1810d-175">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD w programie `Startup.ConfigureServices` przy użyciu `wids` oświadczeń (dobrze znanych identyfikatorów/identyfikatorów szablonów ról):</span><span class="sxs-lookup"><span data-stu-id="1810d-175">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="1810d-176">Aby uzyskać pełną listę identyfikatorów ról administratorów usługi AAD, zobacz [identyfikatory szablonów ról](/azure/active-directory/roles/permissions-reference#role-template-ids) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1810d-176">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="1810d-177">Aby uzyskać więcej informacji na temat zasad autoryzacji, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="1810d-177">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="1810d-178">Dostęp do kontrolera w aplikacji **serwerowej** może opierać się na użyciu [ `[Authorize]` atrybutu](xref:security/authorization/simple) o nazwie zasad (Dokumentacja interfejsu API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).</span><span class="sxs-lookup"><span data-stu-id="1810d-178">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="1810d-179">Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdministrator` :</span><span class="sxs-lookup"><span data-stu-id="1810d-179">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

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

<span data-ttu-id="1810d-180">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="1810d-180">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="1810d-181">Role aplikacji</span><span class="sxs-lookup"><span data-stu-id="1810d-181">App Roles</span></span>

<span data-ttu-id="1810d-182">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia oświadczeń członkostwa ról aplikacji, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1810d-182">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="1810d-183">W poniższym przykładzie przyjęto założenie, że aplikacje **klienta** i **serwera** są skonfigurowane z dwiema rolami, a role są przypisane do użytkownika testowego:</span><span class="sxs-lookup"><span data-stu-id="1810d-183">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="1810d-184">Podczas tworzenia aplikacji hostowanej Blazor WebAssembly lub pary klient-serwer aplikacji autonomicznych (autonomiczna Blazor WebAssembly aplikacja i autonomiczna aplikacja interfejsu api serwera ASP.NET Core/aplikacji interfejsu API sieci Web), `appRoles` Właściwość manifestu zarówno klienta, jak i serwera Azure Portal rejestracji aplikacji musi zawierać te same skonfigurowane role.</span><span class="sxs-lookup"><span data-stu-id="1810d-184">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="1810d-185">Po ustanowieniu ról w manifeście aplikacji klienta skopiuj je w całości do manifestu aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="1810d-185">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="1810d-186">Jeśli nie zdublowano manifestu `appRoles` między rejestracjami aplikacji klienta i serwera, oświadczenia ról nie są ustanawiane dla uwierzytelnionych użytkowników interfejsu API serwera/interfejsu API sieci Web, nawet jeśli ich token dostępu ma poprawne oświadczenia ról.</span><span class="sxs-lookup"><span data-stu-id="1810d-186">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="1810d-187">Chociaż nie można przypisywać ról do grup bez konta Azure AD — wersja Premium, można przypisywać role do użytkowników i odbierać role dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1810d-187">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="1810d-188">Wskazówki zawarte w tej sekcji nie wymagają konta usługi AAD Premium.</span><span class="sxs-lookup"><span data-stu-id="1810d-188">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="1810d-189">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="1810d-189">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="1810d-190">`CustomAccountFactory`Pokazane w sekcji [niestandardowe konto użytkownika](#custom-user-account) jest skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="1810d-190">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="1810d-191">Dodaj i zarejestruj `CustomAccountFactory` w aplikacji **klienckiej** , jak pokazano w sekcji [niestandardowe konto użytkownika](#custom-user-account) .</span><span class="sxs-lookup"><span data-stu-id="1810d-191">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="1810d-192">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="1810d-192">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1810d-193">W `Program.Main` aplikacji **klienckiej** Określ wartość " `appRole` " jako rolę roszczeń dla <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> kontroli:</span><span class="sxs-lookup"><span data-stu-id="1810d-193">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="1810d-194">Jeśli wolisz używać `directoryRoles` roszczeń (Dodaj role administratorów), Przypisz " `directoryRoles` " do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1810d-194">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="1810d-195">W `Startup.ConfigureServices` aplikacji **serwerowej** Określ wartość " `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` " jako rolę roszczeń dla <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> kontroli:</span><span class="sxs-lookup"><span data-stu-id="1810d-195">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

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
> <span data-ttu-id="1810d-196">Jeśli wolisz używać `wids` roszczeń (Dodaj role administratorów), Przypisz " `wids` " do <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1810d-196">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="1810d-197">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="1810d-197">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="1810d-198">Każdy mechanizm autoryzacji w składnikach aplikacji **klienckiej** może używać `admin` roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1810d-198">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="1810d-199">`AuthorizeView` składnika</span><span class="sxs-lookup"><span data-stu-id="1810d-199">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="1810d-200">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="1810d-200">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="1810d-201">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="1810d-201">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="1810d-202">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="1810d-202">Multiple role tests are supported:</span></span>

* <span data-ttu-id="1810d-203">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `AuthorizeView` składnikiem:</span><span class="sxs-lookup"><span data-stu-id="1810d-203">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="1810d-204">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` ze `AuthorizeView` składnikiem:</span><span class="sxs-lookup"><span data-stu-id="1810d-204">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="1810d-205">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="1810d-205">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="1810d-206">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="1810d-206">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="1810d-207">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z kodem proceduralnym:</span><span class="sxs-lookup"><span data-stu-id="1810d-207">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="1810d-208">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z kodem proceduralnym poprzez zmianę [warunku or ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) na [Conditional i ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1810d-208">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="1810d-209">Każdy mechanizm autoryzacji w kontrolerach aplikacji **serwera** może używać `admin` roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1810d-209">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="1810d-210">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="1810d-210">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="1810d-211">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="1810d-211">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="1810d-212">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="1810d-212">Multiple role tests are supported:</span></span>

* <span data-ttu-id="1810d-213">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="1810d-213">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="1810d-214">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="1810d-214">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="1810d-215">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z kodem proceduralnym:</span><span class="sxs-lookup"><span data-stu-id="1810d-215">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="1810d-216">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z kodem proceduralnym poprzez zmianę [warunku or ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) na [Conditional i ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1810d-216">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="1810d-217">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1810d-217">Additional resources</span></span>

* [<span data-ttu-id="1810d-218">Identyfikatory szablonów ról (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="1810d-218">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="1810d-219">`groupMembershipClaims` Attribute (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="1810d-219">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="1810d-220">Instrukcje: Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="1810d-220">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="1810d-221">Role aplikacji (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="1810d-221">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
