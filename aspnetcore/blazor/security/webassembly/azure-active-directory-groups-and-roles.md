---
title: ASP.NET Core Blazor WebAssembly z grupami Azure Active Directory i rolami
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor WebAssembly program, aby używać grup i ról Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
no-loc:
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
ms.openlocfilehash: bd0a7c5a905ae4888ea6ad326be5b16cbfaa10fc
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130408"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="0aeba-103">Grupy usługi Azure AD, role administracyjne i role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0aeba-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="0aeba-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0aeba-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="0aeba-105">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="0aeba-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="0aeba-106">Grupy zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0aeba-106">User-defined groups</span></span>
  * <span data-ttu-id="0aeba-107">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="0aeba-107">Security</span></span>
  * <span data-ttu-id="0aeba-108">O365</span><span class="sxs-lookup"><span data-stu-id="0aeba-108">O365</span></span>
  * <span data-ttu-id="0aeba-109">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="0aeba-109">Distribution</span></span>
* <span data-ttu-id="0aeba-110">Role</span><span class="sxs-lookup"><span data-stu-id="0aeba-110">Roles</span></span>
  * <span data-ttu-id="0aeba-111">Wbudowane role administracyjne</span><span class="sxs-lookup"><span data-stu-id="0aeba-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="0aeba-112">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0aeba-112">User-defined roles</span></span>

<span data-ttu-id="0aeba-113">Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="0aeba-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="0aeba-114">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="0aeba-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="0aeba-115">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="0aeba-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="0aeba-116">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="0aeba-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="0aeba-117">Uprawnienie Microsoft Graph interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0aeba-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="0aeba-118">Wywołanie [interfejsu API Microsoft Graph](/graph/use-the-api) jest wymagane dla każdego użytkownika aplikacji mającego więcej niż pięć wbudowanej roli administratora usługi AAD i członkostwa w grupie zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="0aeba-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="0aeba-119">Aby zezwolić na interfejs API programu Graph wywołań, nadaj aplikacji autonomicznej lub klienckiej w hostowanym Blazor rozwiązaniu dowolne z następujących [uprawnień interfejs API programu Graph](/graph/permissions-reference) w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="0aeba-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="0aeba-120">`Directory.Read.All`jest uprawnieniem z najniższymi uprawnieniami i jest uprawnieniem używanym do przykładu opisanego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="0aeba-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="0aeba-121">Grupy zdefiniowane przez użytkownika i wbudowane role administracyjne</span><span class="sxs-lookup"><span data-stu-id="0aeba-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="0aeba-122">Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0aeba-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="0aeba-123">Przypisz użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i wbudowanych ról administracyjnych.</span><span class="sxs-lookup"><span data-stu-id="0aeba-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="0aeba-124">Role korzystające z grup zabezpieczeń usługi Azure AD</span><span class="sxs-lookup"><span data-stu-id="0aeba-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="0aeba-125">`groupMembershipClaims`przypisane</span><span class="sxs-lookup"><span data-stu-id="0aeba-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="0aeba-126">W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="0aeba-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="0aeba-127">Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0aeba-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="0aeba-128">Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="0aeba-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="0aeba-129">Gdy liczba przypisanych wbudowanych ról administracyjnych platformy Azure i grup zdefiniowanych przez użytkownika przekracza pięć, usługa AAD wysyła do niego zgłoszenie, `hasgroups` `true` a nie wysyła `groups` roszczeń.</span><span class="sxs-lookup"><span data-stu-id="0aeba-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="0aeba-130">Wszystkie aplikacje, które mogą mieć więcej niż pięć ról i grup przypisanych do swoich użytkowników, muszą wykonać oddzielne wywołanie interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="0aeba-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="0aeba-131">Przykładowa implementacja podana w tym artykule dotyczy tego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="0aeba-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="0aeba-132">Aby uzyskać więcej informacji, zobacz `groups` `hasgroups` artykuł i informacje o oświadczeniach w [tokenach dostępu do platformy tożsamości firmy Microsoft: oświadczenie dotyczące ładunku](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="0aeba-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="0aeba-133"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.</span><span class="sxs-lookup"><span data-stu-id="0aeba-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="0aeba-134">Przypisz pustą tablicę do każdej właściwości, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach później.</span><span class="sxs-lookup"><span data-stu-id="0aeba-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="0aeba-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="0aeba-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="0aeba-136">W aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego Blazor Utwórz klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> .</span><span class="sxs-lookup"><span data-stu-id="0aeba-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="0aeba-137">Użyj poprawnego zakresu (uprawnienia) dla wywołań interfejs API programu Graph, które uzyskują informacje o rolach i grupach.</span><span class="sxs-lookup"><span data-stu-id="0aeba-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="0aeba-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="0aeba-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="0aeba-139">W programie `Program.Main` ( `Program.cs` ) Dodaj <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> usługę implementacji i Dodaj nazwę <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="0aeba-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="0aeba-140">Poniższy przykład nazywa klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="0aeba-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="0aeba-141">Utwórz klasy obiektów katalogu usługi AAD, aby otrzymywać role i grupy protokołu Open Data Protocol (OData) z wywołania interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="0aeba-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="0aeba-142">Dane OData docierają do formatu JSON i wywołanie do <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> wypełnienia wystąpienia `DirectoryObjects` klasy.</span><span class="sxs-lookup"><span data-stu-id="0aeba-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="0aeba-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="0aeba-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="0aeba-144">Utwórz niestandardową fabrykę użytkowników, aby przetwarzać oświadczenia ról i grup.</span><span class="sxs-lookup"><span data-stu-id="0aeba-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="0aeba-145">Poniższa przykładowa implementacja obsługuje również `roles` tablicę roszczeń, która znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="0aeba-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="0aeba-146">Jeśli `hasgroups` jest obecny wniosek, nazwa <xref:System.Net.Http.HttpClient> jest używana do autoryzowania żądania interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="0aeba-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="0aeba-147">W tej implementacji jest stosowany Identity punkt końcowy platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([Dokumentacja interfejsu API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="0aeba-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="0aeba-148">Wskazówki zawarte w tym temacie zostaną zaktualizowane dla Identity wersji 2.0, gdy pakiety MSAL zostaną uaktualnione do wersji 2.0.</span><span class="sxs-lookup"><span data-stu-id="0aeba-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="0aeba-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="0aeba-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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
                    var client = _clientFactory.CreateClient("GraphAPI");

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
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="0aeba-150">Nie trzeba podawać kodu, aby usunąć pierwotne `groups` zastrzeżenie, jeśli jest obecny, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="0aeba-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="0aeba-151">Podejście w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0aeba-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="0aeba-152">Dodaje klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , aby dołączyć tokeny dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="0aeba-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="0aeba-153">Dodaje nazwane <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejsu API sieci Web do bezpiecznego, zewnętrznego punktu końcowego interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0aeba-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="0aeba-154">Używa nazwy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań.</span><span class="sxs-lookup"><span data-stu-id="0aeba-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="0aeba-155">Ogólny zakres tego podejścia znajduje się w <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artykule.</span><span class="sxs-lookup"><span data-stu-id="0aeba-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="0aeba-156">Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) aplikacji autonomicznej lub aplikacji klienta hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="0aeba-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="0aeba-157">Wyrażanie zgody na `Directory.Read.All` zakres uprawnień jako dodatkowy zakres dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0aeba-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="0aeba-158">Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="0aeba-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="0aeba-159">Poniższy przykład tworzy zasady dla wbudowanej roli *administratora rozliczeń* w usłudze AAD:</span><span class="sxs-lookup"><span data-stu-id="0aeba-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="0aeba-160">Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory grup ról usługi AAD administracyjnych](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="0aeba-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="0aeba-161">W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0aeba-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="0aeba-162">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="0aeba-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="0aeba-163">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="0aeba-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="0aeba-164">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="0aeba-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="0aeba-165">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="0aeba-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="0aeba-166">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0aeba-166">User-defined roles</span></span>

<span data-ttu-id="0aeba-167">Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0aeba-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="0aeba-168">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0aeba-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="0aeba-169">W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:</span><span class="sxs-lookup"><span data-stu-id="0aeba-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="0aeba-170">Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0aeba-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="0aeba-171">Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.</span><span class="sxs-lookup"><span data-stu-id="0aeba-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="0aeba-172">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="0aeba-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="0aeba-173">Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0aeba-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="0aeba-174">Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="0aeba-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="0aeba-175">`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0aeba-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="0aeba-176">Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub aplikacji klienckiej rozwiązania hostowanego Blazor , jak pokazano w sekcji [zdefiniowane przez użytkownika grupy i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="0aeba-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="0aeba-177">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="0aeba-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="0aeba-178">W aplikacji `Program.Main` autonomicznej lub aplikacji klienckiej rozwiązania hostowanego Blazor należy określić wartość " `role` " jako rolę żądania:</span><span class="sxs-lookup"><span data-stu-id="0aeba-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="0aeba-179">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="0aeba-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="0aeba-180">Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0aeba-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="0aeba-181">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="0aeba-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="0aeba-182">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="0aeba-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="0aeba-183">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="0aeba-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="0aeba-184">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="0aeba-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="0aeba-185">Identyfikatory grup ról administracyjnych usługi AAD</span><span class="sxs-lookup"><span data-stu-id="0aeba-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="0aeba-186">Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="0aeba-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="0aeba-187">Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0aeba-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="0aeba-188">Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i wbudowane role administracyjne usługi AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="0aeba-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="0aeba-189">Rola administracyjna usługi AAD</span><span class="sxs-lookup"><span data-stu-id="0aeba-189">AAD Administrative Role</span></span> | <span data-ttu-id="0aeba-190">Identyfikator obiektu</span><span class="sxs-lookup"><span data-stu-id="0aeba-190">Object ID</span></span>
--- | ---
<span data-ttu-id="0aeba-191">Administrator aplikacji</span><span class="sxs-lookup"><span data-stu-id="0aeba-191">Application administrator</span></span> | <span data-ttu-id="0aeba-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="0aeba-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="0aeba-193">Deweloper aplikacji</span><span class="sxs-lookup"><span data-stu-id="0aeba-193">Application developer</span></span> | <span data-ttu-id="0aeba-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="0aeba-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="0aeba-195">Administrator uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0aeba-195">Authentication administrator</span></span> | <span data-ttu-id="0aeba-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="0aeba-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="0aeba-197">Administrator usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="0aeba-197">Azure DevOps administrator</span></span> | <span data-ttu-id="0aeba-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="0aeba-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="0aeba-199">Azure Information Protection administrator</span><span class="sxs-lookup"><span data-stu-id="0aeba-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="0aeba-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="0aeba-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="0aeba-201">Administrator zestawu kluczy B2C IEF</span><span class="sxs-lookup"><span data-stu-id="0aeba-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="0aeba-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="0aeba-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="0aeba-203">Administrator zasad B2C IEF</span><span class="sxs-lookup"><span data-stu-id="0aeba-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="0aeba-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="0aeba-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="0aeba-205">Administrator przepływu użytkownika B2C</span><span class="sxs-lookup"><span data-stu-id="0aeba-205">B2C user flow administrator</span></span> | <span data-ttu-id="0aeba-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="0aeba-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="0aeba-207">B2C atrybutu przepływu użytkownika</span><span class="sxs-lookup"><span data-stu-id="0aeba-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="0aeba-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="0aeba-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="0aeba-209">Administrator rozliczeń</span><span class="sxs-lookup"><span data-stu-id="0aeba-209">Billing administrator</span></span> | <span data-ttu-id="0aeba-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="0aeba-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="0aeba-211">Administrator aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="0aeba-211">Cloud application administrator</span></span> | <span data-ttu-id="0aeba-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="0aeba-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="0aeba-213">Administrator urządzenia w chmurze</span><span class="sxs-lookup"><span data-stu-id="0aeba-213">Cloud device administrator</span></span> | <span data-ttu-id="0aeba-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="0aeba-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="0aeba-215">Administrator zgodności</span><span class="sxs-lookup"><span data-stu-id="0aeba-215">Compliance administrator</span></span> | <span data-ttu-id="0aeba-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="0aeba-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="0aeba-217">Administrator danych zgodności</span><span class="sxs-lookup"><span data-stu-id="0aeba-217">Compliance data administrator</span></span> | <span data-ttu-id="0aeba-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="0aeba-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="0aeba-219">Administrator dostępu warunkowego</span><span class="sxs-lookup"><span data-stu-id="0aeba-219">Conditional Access administrator</span></span> | <span data-ttu-id="0aeba-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="0aeba-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="0aeba-221">Osoba zatwierdzająca dostęp do skrytki klienta</span><span class="sxs-lookup"><span data-stu-id="0aeba-221">Customer LockBox access approver</span></span> | <span data-ttu-id="0aeba-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="0aeba-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="0aeba-223">Administrator usługi Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="0aeba-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="0aeba-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="0aeba-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="0aeba-225">Czytelnicy katalogów</span><span class="sxs-lookup"><span data-stu-id="0aeba-225">Directory readers</span></span> | <span data-ttu-id="0aeba-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="0aeba-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="0aeba-227">Administrator systemu Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="0aeba-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="0aeba-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="0aeba-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="0aeba-229">Administrator programu Exchange</span><span class="sxs-lookup"><span data-stu-id="0aeba-229">Exchange administrator</span></span> | <span data-ttu-id="0aeba-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="0aeba-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="0aeba-231">IdentityAdministrator dostawcy zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="0aeba-231">External Identity Provider administrator</span></span> | <span data-ttu-id="0aeba-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="0aeba-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="0aeba-233">Administrator globalny</span><span class="sxs-lookup"><span data-stu-id="0aeba-233">Global administrator</span></span> | <span data-ttu-id="0aeba-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="0aeba-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="0aeba-235">Czytnik globalny</span><span class="sxs-lookup"><span data-stu-id="0aeba-235">Global reader</span></span> | <span data-ttu-id="0aeba-236">f6903b21-6aba-4124-B44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="0aeba-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="0aeba-237">Administrator grup</span><span class="sxs-lookup"><span data-stu-id="0aeba-237">Groups administrator</span></span> | <span data-ttu-id="0aeba-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="0aeba-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="0aeba-239">Zapraszający gościa</span><span class="sxs-lookup"><span data-stu-id="0aeba-239">Guest inviter</span></span> | <span data-ttu-id="0aeba-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="0aeba-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="0aeba-241">Administrator pomocy technicznej</span><span class="sxs-lookup"><span data-stu-id="0aeba-241">Helpdesk administrator</span></span> | <span data-ttu-id="0aeba-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="0aeba-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="0aeba-243">Administrator usługi Intune</span><span class="sxs-lookup"><span data-stu-id="0aeba-243">Intune administrator</span></span> | <span data-ttu-id="0aeba-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="0aeba-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="0aeba-245">Usługi kaizala administrator</span><span class="sxs-lookup"><span data-stu-id="0aeba-245">Kaizala administrator</span></span> | <span data-ttu-id="0aeba-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="0aeba-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="0aeba-247">Administrator licencji</span><span class="sxs-lookup"><span data-stu-id="0aeba-247">License administrator</span></span> | <span data-ttu-id="0aeba-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="0aeba-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="0aeba-249">Czytnik prywatności centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="0aeba-249">Message center privacy reader</span></span> | <span data-ttu-id="0aeba-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="0aeba-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="0aeba-251">Czytelnik centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="0aeba-251">Message center reader</span></span> | <span data-ttu-id="0aeba-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="0aeba-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="0aeba-253">Administrator aplikacji pakietu Office</span><span class="sxs-lookup"><span data-stu-id="0aeba-253">Office apps administrator</span></span> | <span data-ttu-id="0aeba-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="0aeba-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="0aeba-255">Administrator haseł</span><span class="sxs-lookup"><span data-stu-id="0aeba-255">Password administrator</span></span> | <span data-ttu-id="0aeba-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="0aeba-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="0aeba-257">Power BI administrator</span><span class="sxs-lookup"><span data-stu-id="0aeba-257">Power BI administrator</span></span> | <span data-ttu-id="0aeba-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="0aeba-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="0aeba-259">platforma Power administrator</span><span class="sxs-lookup"><span data-stu-id="0aeba-259">Power platform administrator</span></span> | <span data-ttu-id="0aeba-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="0aeba-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="0aeba-261">Administrator uprzywilejowanego uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0aeba-261">Privileged authentication administrator</span></span> | <span data-ttu-id="0aeba-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="0aeba-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="0aeba-263">Administrator ról uprzywilejowanych</span><span class="sxs-lookup"><span data-stu-id="0aeba-263">Privileged role administrator</span></span> | <span data-ttu-id="0aeba-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="0aeba-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="0aeba-265">Czytelnik raportów</span><span class="sxs-lookup"><span data-stu-id="0aeba-265">Reports reader</span></span> | <span data-ttu-id="0aeba-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="0aeba-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="0aeba-267">Administrator wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0aeba-267">Search administrator</span></span> | <span data-ttu-id="0aeba-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="0aeba-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="0aeba-269">Edytor wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0aeba-269">Search editor</span></span> | <span data-ttu-id="0aeba-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="0aeba-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="0aeba-271">Administrator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0aeba-271">Security administrator</span></span> | <span data-ttu-id="0aeba-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="0aeba-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="0aeba-273">Operator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0aeba-273">Security operator</span></span> | <span data-ttu-id="0aeba-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="0aeba-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="0aeba-275">Czytelnik zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0aeba-275">Security reader</span></span> | <span data-ttu-id="0aeba-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="0aeba-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="0aeba-277">Administrator pomocy technicznej usługi</span><span class="sxs-lookup"><span data-stu-id="0aeba-277">Service support administrator</span></span> | <span data-ttu-id="0aeba-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="0aeba-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="0aeba-279">Administrator programu SharePoint</span><span class="sxs-lookup"><span data-stu-id="0aeba-279">SharePoint administrator</span></span> | <span data-ttu-id="0aeba-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="0aeba-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="0aeba-281">Administrator programu Skype dla firm</span><span class="sxs-lookup"><span data-stu-id="0aeba-281">Skype for Business administrator</span></span> | <span data-ttu-id="0aeba-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="0aeba-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="0aeba-283">Administratorzy zespołu ds. komunikacji</span><span class="sxs-lookup"><span data-stu-id="0aeba-283">Teams Communications Administrator</span></span> | <span data-ttu-id="0aeba-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="0aeba-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="0aeba-285">Inżynierowie pomocy technicznej dla zespołów</span><span class="sxs-lookup"><span data-stu-id="0aeba-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="0aeba-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="0aeba-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="0aeba-287">Zespoły komunikacyjne specjalisty</span><span class="sxs-lookup"><span data-stu-id="0aeba-287">Teams Communications Specialist</span></span> | <span data-ttu-id="0aeba-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="0aeba-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="0aeba-289">Administrator usługi Teams</span><span class="sxs-lookup"><span data-stu-id="0aeba-289">Teams Service Administrator</span></span> | <span data-ttu-id="0aeba-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="0aeba-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="0aeba-291">Administrator użytkowników</span><span class="sxs-lookup"><span data-stu-id="0aeba-291">User administrator</span></span> | <span data-ttu-id="0aeba-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="0aeba-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0aeba-293">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0aeba-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
