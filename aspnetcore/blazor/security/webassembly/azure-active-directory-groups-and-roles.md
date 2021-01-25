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
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="0f07e-103">Grupy Azure Active Directory (AAD), role administratorów i role aplikacji</span><span class="sxs-lookup"><span data-stu-id="0f07e-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="0f07e-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0f07e-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="0f07e-105">Ten artykuł ma zastosowanie do Blazor ASP.NET Core aplikacji w wersji 3,1 z firmą Microsoft Identity 1,0 i wkrótce zostanie zaktualizowany do 5,0 z Identity 2,0.</span><span class="sxs-lookup"><span data-stu-id="0f07e-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="0f07e-106">Aby uzyskać więcej informacji, zobacz następujący problem z usługą GitHub i żądanie ściągnięcia.</span><span class="sxs-lookup"><span data-stu-id="0f07e-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="0f07e-107">Karta **zmiany plików** w żądaniu ściągnięcia zawiera wersję roboczą tekstu i przykłady aktualizacji artykułu.</span><span class="sxs-lookup"><span data-stu-id="0f07e-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="0f07e-108">Po przejrzeniu i ostatecznych aktualizacjach żądanie ściągnięcia zostanie scalone z zestawem dokumentacji na żywo.</span><span class="sxs-lookup"><span data-stu-id="0f07e-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="0f07e-109">Problem: [ Blazor WASM z grupami i rolami usługi AAD (#17683 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="0f07e-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="0f07e-110">Żądanie ściągnięcia: [ Blazor temat grupy i role usługi AAD 5,0 (#20856 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="0f07e-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="0f07e-111">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="0f07e-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="0f07e-112">Grupy</span><span class="sxs-lookup"><span data-stu-id="0f07e-112">Groups</span></span>
  * <span data-ttu-id="0f07e-113">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="0f07e-113">Security</span></span>
  * <span data-ttu-id="0f07e-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="0f07e-114">Microsoft 365</span></span>
  * <span data-ttu-id="0f07e-115">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="0f07e-115">Distribution</span></span>
* <span data-ttu-id="0f07e-116">Role</span><span class="sxs-lookup"><span data-stu-id="0f07e-116">Roles</span></span>
  * <span data-ttu-id="0f07e-117">Role administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="0f07e-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="0f07e-118">Role aplikacji</span><span class="sxs-lookup"><span data-stu-id="0f07e-118">App Roles</span></span>

<span data-ttu-id="0f07e-119">Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="0f07e-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="0f07e-120">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="0f07e-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="0f07e-121">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="0f07e-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="0f07e-122">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="0f07e-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="0f07e-123">Wskazówki zawarte w artykule zawierają instrukcje dotyczące aplikacji klienta i serwera:</span><span class="sxs-lookup"><span data-stu-id="0f07e-123">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="0f07e-124">**Klient**: Aplikacje autonomiczne Blazor WebAssembly lub *`Client`* aplikacja hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="0f07e-124">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="0f07e-125">**Serwer**: autonomiczny interfejs api serwera ASP.NET Core/aplikacje Web API lub *`Server`* aplikacja hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="0f07e-125">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="0f07e-126">Zakresy</span><span class="sxs-lookup"><span data-stu-id="0f07e-126">Scopes</span></span>

<span data-ttu-id="0f07e-127">Aby zezwolić na [Microsoft Graph wywołań interfejsu API](/graph/use-the-api) dla profilu użytkownika, przypisania roli i danych członkostwa w grupie, **Klient** programu jest skonfigurowany przy użyciu ( `https://graph.microsoft.com/User.Read` ) [interfejs API programu Graph uprawnień (zakres)](/graph/permissions-reference) w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0f07e-127">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="0f07e-128">Aplikacja **serwera** , która wywołuje interfejs API programu Graph dla danych członkostwa roli i grupy `GroupMember.Read.All` () ( `https://graph.microsoft.com/GroupMember.Read.All` ) [interfejs API programu Graph uprawnienia (zakres)](/graph/permissions-reference) w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0f07e-128">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="0f07e-129">Te zakresy są wymagane oprócz zakresów wymaganych w scenariuszach wdrażania usługi AAD opisanych w temacie w pierwszej sekcji tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="0f07e-129">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="0f07e-130">Słowa "uprawnienie" i "zakres" są używane zamiennie w Azure Portal i w różnych zestawach dokumentacji firmy Microsoft i zewnętrznych.</span><span class="sxs-lookup"><span data-stu-id="0f07e-130">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="0f07e-131">W tym artykule użyto słowa "Scope" w ramach uprawnień przypisanych do aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0f07e-131">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="0f07e-132">Atrybut oświadczeń członkostwa w grupie</span><span class="sxs-lookup"><span data-stu-id="0f07e-132">Group Membership Claims attribute</span></span>

<span data-ttu-id="0f07e-133">W manifeście aplikacji w Azure Portal dla aplikacji **klienta** i **serwera** Ustaw [ `groupMembershipClaims` atrybut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) na `All` .</span><span class="sxs-lookup"><span data-stu-id="0f07e-133">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="0f07e-134">Wartość `All` powoduje uzyskanie wszystkich grup zabezpieczeń, grup dystrybucyjnych i ról, do których należy użytkownik zalogowany.</span><span class="sxs-lookup"><span data-stu-id="0f07e-134">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="0f07e-135">Otwórz Azure Portal rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0f07e-135">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="0f07e-136">Wybierz pozycję **Zarządzaj**  >  **manifest** na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="0f07e-136">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="0f07e-137">Znajdź `groupMembershipClaims` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0f07e-137">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="0f07e-138">Ustaw wartość na `All`.</span><span class="sxs-lookup"><span data-stu-id="0f07e-138">Set the value to `All`.</span></span>
1. <span data-ttu-id="0f07e-139">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="0f07e-139">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="0f07e-140">Niestandardowe konto użytkownika</span><span class="sxs-lookup"><span data-stu-id="0f07e-140">Custom user account</span></span>

<span data-ttu-id="0f07e-141">Przypisz użytkowników do grup zabezpieczeń usługi AAD i ról administratorów usługi AAD w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0f07e-141">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="0f07e-142">Przykłady w tym artykule:</span><span class="sxs-lookup"><span data-stu-id="0f07e-142">The examples in this article:</span></span>

* <span data-ttu-id="0f07e-143">Załóżmy, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi aad w Azure Portal dzierżawy usługi AAD na potrzeby autoryzacji dostępu do danych interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="0f07e-143">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="0f07e-144">Użyj [zasad autoryzacji](xref:security/authorization/policies) , aby kontrolować dostęp w aplikacjach **klienta** i **serwera** .</span><span class="sxs-lookup"><span data-stu-id="0f07e-144">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="0f07e-145">W aplikacji **klienckiej** rozwiń, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Aby uwzględnić właściwości dla:</span><span class="sxs-lookup"><span data-stu-id="0f07e-145">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="0f07e-146">`Roles`: Tablica ról aplikacji usługi AAD (pokryte w sekcji [role aplikacji](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="0f07e-146">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="0f07e-147">`Wids`: Role administratora usługi AAD w [oświadczeniach dobrze znanych identyfikatorów ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="0f07e-147">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="0f07e-148">`Oid`: Niezmiennego [żądania identyfikatora obiektu ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (unikatowa identyfikacja użytkownika w ramach dzierżaw i między dzierżawcami)</span><span class="sxs-lookup"><span data-stu-id="0f07e-148">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="0f07e-149">Przypisz pustą tablicę do każdej właściwości tablicy, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach.</span><span class="sxs-lookup"><span data-stu-id="0f07e-149">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="0f07e-150">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="0f07e-150">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="0f07e-151">Dodaj odwołanie do pakietu do pliku projektu aplikacji **klienta** dla programu [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="0f07e-151">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="0f07e-152">Dodaj klasy i konfigurację narzędzia zestawu Graph SDK w sekcji *zestaw Graph SDK* <xref:blazor/security/webassembly/graph-api#graph-sdk> artykułu.</span><span class="sxs-lookup"><span data-stu-id="0f07e-152">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="0f07e-153">W `GraphClientExtensions` klasie Określ `User.Read` zakres dla tokenu dostępu w `AuthenticateRequestAsync` metodzie:</span><span class="sxs-lookup"><span data-stu-id="0f07e-153">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="0f07e-154">Dodaj następującą niestandardową fabrykę kont użytkowników do aplikacji **klienckiej** .</span><span class="sxs-lookup"><span data-stu-id="0f07e-154">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="0f07e-155">Niestandardowa fabryka użytkowników służy do ustanawiania:</span><span class="sxs-lookup"><span data-stu-id="0f07e-155">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="0f07e-156">Oświadczenia ról aplikacji ( `appRole` ) (omówione w sekcji [role aplikacji](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="0f07e-156">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="0f07e-157">Oświadczenia roli administratora usługi AAD ( `directoryRole` )</span><span class="sxs-lookup"><span data-stu-id="0f07e-157">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="0f07e-158">Przykładowe zdarzenie dotyczące danych profilu użytkownika dla numeru telefonu komórkowego użytkownika ( `mobilePhone` )</span><span class="sxs-lookup"><span data-stu-id="0f07e-158">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="0f07e-159">Oświadczenia grupy usługi AAD ( `directoryGroup` )</span><span class="sxs-lookup"><span data-stu-id="0f07e-159">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="0f07e-160">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="0f07e-160">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="0f07e-161">Poprzedzający kod nie obejmuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="0f07e-161">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="0f07e-162">Jeśli aplikacja wymaga bezpośrednich i przechodnich oświadczeń członkostwa w grupie, Zamień `MemberOf` Właściwość ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) na `TransitiveMemberOf` () `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0f07e-162">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="0f07e-163">Powyższy kod ignoruje oświadczenia członkostwa w grupie ( `groups` ), które są rolami administratora usługi AAD ( `#microsoft.graph.directoryRole` Typ), ponieważ wartości identyfikatorów GUID zwracane przez Identity platformę Microsoft 2,0 to **identyfikatory jednostek** roli administratora usługi AAD, a nie [**identyfikatory szablonu roli**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="0f07e-163">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="0f07e-164">Identyfikatory jednostek nie są stabilne wśród dzierżawców na Identity platformie Microsoft Platform 2,0 i nie powinny być używane do tworzenia zasad autoryzacji dla użytkowników w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="0f07e-164">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="0f07e-165">Zawsze używaj **identyfikatorów szablonów ról** dla ról administratorów usługi AAD **dostarczonych przez `wids` oświadczenia**.</span><span class="sxs-lookup"><span data-stu-id="0f07e-165">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="0f07e-166">W `Program.Main` aplikacji **klienckiej** Skonfiguruj uwierzytelnianie MSAL, tak aby używało fabryki kont użytkowników niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="0f07e-166">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="0f07e-167">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0f07e-167">`Program.cs`:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="0f07e-168">Konfiguracja autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0f07e-168">Authorization configuration</span></span>

<span data-ttu-id="0f07e-169">W aplikacji **klienckiej** Utwórz [zasady](xref:security/authorization/policies) dla każdej [roli aplikacji](#app-roles), roli administratora usługi AAD lub grupy zabezpieczeń w temacie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="0f07e-169">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="0f07e-170">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="0f07e-170">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="0f07e-171">Aby uzyskać pełną listę identyfikatorów ról administratorów usługi AAD, zobacz [identyfikatory szablonów ról](/azure/active-directory/roles/permissions-reference#role-template-ids) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0f07e-171">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="0f07e-172">Aby uzyskać więcej informacji na temat zasad autoryzacji, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="0f07e-172">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0f07e-173">W poniższych przykładach aplikacja **kliencka** używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0f07e-173">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="0f07e-174">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="0f07e-174">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="0f07e-175">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="0f07e-175">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="0f07e-176">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="0f07e-176">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="0f07e-177">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="0f07e-177">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="0f07e-178">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="0f07e-178">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="0f07e-179">Autoryzuj dostęp do interfejsu API serwera/dostępu do interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="0f07e-179">Authorize server API/web API access</span></span>

<span data-ttu-id="0f07e-180">Aplikacja interfejsu API **serwera** może autoryzować użytkowników do uzyskiwania dostępu do bezpiecznych punktów końcowych interfejsu API przy użyciu [zasad autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń, ról administratorów usługi AAD i ról aplikacji, gdy token dostępu zawiera `groups` , `wids` i `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="0f07e-180">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="0f07e-181">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD w programie `Startup.ConfigureServices` przy użyciu `wids` oświadczeń (dobrze znanych identyfikatorów/identyfikatorów szablonów ról):</span><span class="sxs-lookup"><span data-stu-id="0f07e-181">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="0f07e-182">Aby uzyskać pełną listę identyfikatorów ról administratorów usługi AAD, zobacz [identyfikatory szablonów ról](/azure/active-directory/roles/permissions-reference#role-template-ids) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0f07e-182">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="0f07e-183">Aby uzyskać więcej informacji na temat zasad autoryzacji, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="0f07e-183">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0f07e-184">Dostęp do kontrolera w aplikacji **serwerowej** może opierać się na użyciu [ `[Authorize]` atrybutu](xref:security/authorization/simple) o nazwie zasad (Dokumentacja interfejsu API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).</span><span class="sxs-lookup"><span data-stu-id="0f07e-184">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="0f07e-185">Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdministrator` :</span><span class="sxs-lookup"><span data-stu-id="0f07e-185">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

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

<span data-ttu-id="0f07e-186">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="0f07e-186">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="0f07e-187">Role aplikacji</span><span class="sxs-lookup"><span data-stu-id="0f07e-187">App Roles</span></span>

<span data-ttu-id="0f07e-188">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia oświadczeń członkostwa ról aplikacji, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0f07e-188">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="0f07e-189">W poniższym przykładzie przyjęto założenie, że aplikacje **klienta** i **serwera** są skonfigurowane z dwiema rolami, a role są przypisane do użytkownika testowego:</span><span class="sxs-lookup"><span data-stu-id="0f07e-189">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="0f07e-190">Podczas tworzenia aplikacji hostowanej Blazor WebAssembly lub pary klient-serwer aplikacji autonomicznych (autonomiczna Blazor WebAssembly aplikacja i autonomiczna aplikacja interfejsu api serwera ASP.NET Core/aplikacji interfejsu API sieci Web), `appRoles` Właściwość manifestu zarówno klienta, jak i serwera Azure Portal rejestracji aplikacji musi zawierać te same skonfigurowane role.</span><span class="sxs-lookup"><span data-stu-id="0f07e-190">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="0f07e-191">Po ustanowieniu ról w manifeście aplikacji klienta skopiuj je w całości do manifestu aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="0f07e-191">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="0f07e-192">Jeśli nie zdublowano manifestu `appRoles` między rejestracjami aplikacji klienta i serwera, oświadczenia ról nie są ustanawiane dla uwierzytelnionych użytkowników interfejsu API serwera/interfejsu API sieci Web, nawet jeśli ich token dostępu ma poprawne oświadczenia ról.</span><span class="sxs-lookup"><span data-stu-id="0f07e-192">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="0f07e-193">Chociaż nie można przypisywać ról do grup bez konta Azure AD — wersja Premium, można przypisywać role do użytkowników i odbierać role dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0f07e-193">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="0f07e-194">Wskazówki zawarte w tej sekcji nie wymagają konta usługi AAD Premium.</span><span class="sxs-lookup"><span data-stu-id="0f07e-194">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="0f07e-195">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="0f07e-195">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="0f07e-196">`CustomAccountFactory`Pokazane w sekcji [niestandardowe konto użytkownika](#custom-user-account) jest skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0f07e-196">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="0f07e-197">Dodaj i zarejestruj `CustomAccountFactory` w aplikacji **klienckiej** , jak pokazano w sekcji [niestandardowe konto użytkownika](#custom-user-account) .</span><span class="sxs-lookup"><span data-stu-id="0f07e-197">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="0f07e-198">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="0f07e-198">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="0f07e-199">W `Program.Main` aplikacji **klienckiej** Określ wartość " `appRole` " jako rolę roszczeń dla <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> kontroli:</span><span class="sxs-lookup"><span data-stu-id="0f07e-199">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="0f07e-200">Jeśli wolisz używać `directoryRoles` roszczeń (Dodaj role administratorów), Przypisz " `directoryRoles` " do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0f07e-200">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0f07e-201">W `Startup.ConfigureServices` aplikacji **serwerowej** Określ wartość " `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` " jako rolę roszczeń dla <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> kontroli:</span><span class="sxs-lookup"><span data-stu-id="0f07e-201">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

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
> <span data-ttu-id="0f07e-202">Jeśli wolisz używać `wids` roszczeń (Dodaj role administratorów), Przypisz " `wids` " do <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0f07e-202">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0f07e-203">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="0f07e-203">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="0f07e-204">Każdy mechanizm autoryzacji w składnikach aplikacji **klienckiej** może używać `admin` roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0f07e-204">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="0f07e-205">`AuthorizeView` składnika</span><span class="sxs-lookup"><span data-stu-id="0f07e-205">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="0f07e-206">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="0f07e-206">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="0f07e-207">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="0f07e-207">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="0f07e-208">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="0f07e-208">Multiple role tests are supported:</span></span>

* <span data-ttu-id="0f07e-209">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `AuthorizeView` składnikiem:</span><span class="sxs-lookup"><span data-stu-id="0f07e-209">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="0f07e-210">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` ze `AuthorizeView` składnikiem:</span><span class="sxs-lookup"><span data-stu-id="0f07e-210">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="0f07e-211">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="0f07e-211">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="0f07e-212">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="0f07e-212">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="0f07e-213">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z kodem proceduralnym:</span><span class="sxs-lookup"><span data-stu-id="0f07e-213">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="0f07e-214">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z kodem proceduralnym poprzez zmianę [warunku or ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) na [Conditional i ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0f07e-214">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="0f07e-215">Każdy mechanizm autoryzacji w kontrolerach aplikacji **serwera** może używać `admin` roli do autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0f07e-215">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="0f07e-216">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="0f07e-216">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="0f07e-217">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="0f07e-217">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="0f07e-218">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="0f07e-218">Multiple role tests are supported:</span></span>

* <span data-ttu-id="0f07e-219">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="0f07e-219">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="0f07e-220">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z `[Authorize]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="0f07e-220">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="0f07e-221">Wymagaj, aby użytkownik znajdował **się** w `admin` roli **lub** `developer` z kodem proceduralnym:</span><span class="sxs-lookup"><span data-stu-id="0f07e-221">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="0f07e-222">Wymagaj, aby użytkownik znajdował się **zarówno** w `admin` roli **, jak i** `developer` z kodem proceduralnym poprzez zmianę [warunku or ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) na [Conditional i ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0f07e-222">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="0f07e-223">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0f07e-223">Additional resources</span></span>

* [<span data-ttu-id="0f07e-224">Identyfikatory szablonów ról (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="0f07e-224">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="0f07e-225">`groupMembershipClaims` Attribute (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="0f07e-225">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="0f07e-226">Instrukcje: Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="0f07e-226">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="0f07e-227">Role aplikacji (dokumentacja platformy Azure)</span><span class="sxs-lookup"><span data-stu-id="0f07e-227">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
