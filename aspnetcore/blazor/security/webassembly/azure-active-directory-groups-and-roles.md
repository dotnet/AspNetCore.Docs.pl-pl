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
ms.openlocfilehash: ac666a4c7493140d4ae93047e18202c3d8314c7b
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900703"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="a44a9-103">Grupy Azure Active Directory (AAD), role administratorów i role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="a44a9-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="a44a9-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a44a9-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="a44a9-105">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="a44a9-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a44a9-106">Grupy zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="a44a9-106">User-defined groups</span></span>
  * <span data-ttu-id="a44a9-107">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="a44a9-107">Security</span></span>
  * <span data-ttu-id="a44a9-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="a44a9-108">Microsoft 365</span></span>
  * <span data-ttu-id="a44a9-109">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="a44a9-109">Distribution</span></span>
* <span data-ttu-id="a44a9-110">Role</span><span class="sxs-lookup"><span data-stu-id="a44a9-110">Roles</span></span>
  * <span data-ttu-id="a44a9-111">Role administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="a44a9-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="a44a9-112">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="a44a9-112">User-defined roles</span></span>

<span data-ttu-id="a44a9-113">Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="a44a9-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="a44a9-114">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="a44a9-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="a44a9-115">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="a44a9-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="a44a9-116">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="a44a9-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="a44a9-117">Uprawnienie Microsoft Graph interfejsu API</span><span class="sxs-lookup"><span data-stu-id="a44a9-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="a44a9-118">Wywołanie [interfejsu API Microsoft Graph](/graph/use-the-api) jest wymagane dla wszystkich użytkowników aplikacji mających więcej niż pięć ról administratora usługi AAD i członkostwa w grupach zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a44a9-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="a44a9-119">Aby zezwolić na interfejs API programu Graph wywołań, nadaj Autonomicznemu lub *`Client`* aplikacji hostowanemu Blazor rozwiązaniu dowolne z następujących [uprawnień interfejs API programu Graph](/graph/permissions-reference) w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a44a9-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="a44a9-120">`Directory.Read.All` jest uprawnieniem z najniższymi uprawnieniami i jest uprawnieniem używanym do przykładu opisanego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="a44a9-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a44a9-121">Grupy zdefiniowane przez użytkownika i role administratorów</span><span class="sxs-lookup"><span data-stu-id="a44a9-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a44a9-122">Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a44a9-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="a44a9-123">Przypisywanie użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i ról administratorów usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="a44a9-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="a44a9-124">Role korzystające z grup zabezpieczeń usługi Azure AD</span><span class="sxs-lookup"><span data-stu-id="a44a9-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="a44a9-125">`groupMembershipClaims` przypisane</span><span class="sxs-lookup"><span data-stu-id="a44a9-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="a44a9-126">W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="a44a9-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="a44a9-127">Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="a44a9-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="a44a9-128">Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="a44a9-129">Gdy liczba przypisanych ról administratora usługi AAD i grup zdefiniowanych przez użytkownika przekracza pięć, w usłudze AAD zostanie wysłane zgłoszenie do `hasgroups` `true` wartości zamiast wysyłania `groups` żądania.</span><span class="sxs-lookup"><span data-stu-id="a44a9-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="a44a9-130">Wszystkie aplikacje, które mogą mieć więcej niż pięć ról i grup przypisanych do swoich użytkowników, muszą wykonać oddzielne wywołanie interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a44a9-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="a44a9-131">Przykładowa implementacja podana w tym artykule dotyczy tego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="a44a9-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="a44a9-132">Aby uzyskać więcej informacji, zobacz `groups` `hasgroups` artykuł i informacje o oświadczeniach w [tokenach dostępu do platformy tożsamości firmy Microsoft: oświadczenie dotyczące ładunku](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="a44a9-133"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.</span><span class="sxs-lookup"><span data-stu-id="a44a9-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="a44a9-134">Przypisz pustą tablicę do każdej właściwości, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach później.</span><span class="sxs-lookup"><span data-stu-id="a44a9-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="a44a9-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="a44a9-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="a44a9-136">W aplikacji autonomicznej lub *`Client`* aplikacji rozwiązania hostowanego Blazor Utwórz klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> .</span><span class="sxs-lookup"><span data-stu-id="a44a9-136">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="a44a9-137">Użyj poprawnego zakresu (uprawnienia) dla wywołań interfejs API programu Graph, które uzyskują informacje o rolach i grupach.</span><span class="sxs-lookup"><span data-stu-id="a44a9-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="a44a9-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="a44a9-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="a44a9-139">W programie `Program.Main` ( `Program.cs` ) Dodaj <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> usługę implementacji i Dodaj nazwę <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="a44a9-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="a44a9-140">Poniższy przykład nazywa klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="a44a9-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="a44a9-141">Utwórz klasy obiektów katalogu usługi AAD, aby otrzymywać role i grupy protokołu Open Data Protocol (OData) z wywołania interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="a44a9-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="a44a9-142">Dane OData docierają do formatu JSON i wywołanie do <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> wypełnienia wystąpienia `DirectoryObjects` klasy.</span><span class="sxs-lookup"><span data-stu-id="a44a9-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="a44a9-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="a44a9-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="a44a9-144">Utwórz niestandardową fabrykę użytkowników, aby przetwarzać oświadczenia ról i grup.</span><span class="sxs-lookup"><span data-stu-id="a44a9-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="a44a9-145">Poniższa przykładowa implementacja obsługuje również `roles` tablicę roszczeń, która znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a44a9-146">Jeśli `hasgroups` jest obecny wniosek, nazwa <xref:System.Net.Http.HttpClient> jest używana do autoryzowania żądania interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a44a9-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="a44a9-147">W tej implementacji jest stosowany Identity punkt końcowy platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([Dokumentacja interfejsu API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="a44a9-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="a44a9-148">Wskazówki zawarte w tym temacie zostaną zaktualizowane dla Identity wersji 2.0, gdy pakiety MSAL zostaną uaktualnione do wersji 2.0.</span><span class="sxs-lookup"><span data-stu-id="a44a9-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="a44a9-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a44a9-149">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="a44a9-150">Nie trzeba podawać kodu, aby usunąć pierwotne `groups` zastrzeżenie, jeśli jest obecny, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="a44a9-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="a44a9-151">Podejście w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a44a9-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="a44a9-152">Dodaje klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , aby dołączyć tokeny dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="a44a9-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="a44a9-153">Dodaje nazwane <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejsu API sieci Web do bezpiecznego, zewnętrznego punktu końcowego interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a44a9-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="a44a9-154">Używa nazwy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań.</span><span class="sxs-lookup"><span data-stu-id="a44a9-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="a44a9-155">Ogólny zakres tego podejścia znajduje się w <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artykule.</span><span class="sxs-lookup"><span data-stu-id="a44a9-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="a44a9-156">Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) autonomicznej aplikacji lub *`Client`* aplikacji hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="a44a9-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="a44a9-157">Wyrażanie zgody na `Directory.Read.All` zakres uprawnień jako dodatkowy zakres dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44a9-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="a44a9-158">Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="a44a9-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="a44a9-159">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="a44a9-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a44a9-160">Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="a44a9-161">W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a44a9-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="a44a9-162">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="a44a9-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="a44a9-163">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="a44a9-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="a44a9-164">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="a44a9-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="a44a9-165">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="a44a9-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a44a9-166">Autoryzuj dostęp do interfejsu API serwera dla grup zdefiniowanych przez użytkownika i ról administratorów</span><span class="sxs-lookup"><span data-stu-id="a44a9-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a44a9-167">Oprócz autoryzowania użytkowników w aplikacji webassembly po stronie klienta w celu uzyskania dostępu do stron i zasobów, interfejs API serwera może autoryzować użytkowników w celu uzyskania dostępu do bezpiecznych punktów końcowych interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="a44a9-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="a44a9-168">Gdy aplikacja *serwera* zweryfikuje token dostępu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a44a9-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="a44a9-169">Aplikacja używa niezmiennego [identyfikatora obiektu użytkownika ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) z tokenu JWT ( `id_token` ) w celu uzyskania tokenu dostępu dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="a44a9-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="a44a9-170">Wywołanie interfejs API programu Graph umożliwia uzyskanie członkostwa w roli administratora i grupy zabezpieczeń zdefiniowane przez użytkownika platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a44a9-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="a44a9-171">Członkostwa są używane do ustanawiania `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="a44a9-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="a44a9-172">[Zasady autoryzacji](xref:security/authorization/policies) mogą służyć do ograniczania dostępu użytkowników do punktów końcowych interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="a44a9-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="a44a9-173">Te wskazówki nie obejmują obecnie autoryzacji użytkowników na podstawie [ról zdefiniowanych przez użytkownika usługi AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="a44a9-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="a44a9-174">Pakiety</span><span class="sxs-lookup"><span data-stu-id="a44a9-174">Packages</span></span>

<span data-ttu-id="a44a9-175">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="a44a9-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a44a9-176">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="a44a9-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a44a9-177">[Firma Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="a44a9-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="a44a9-178">Konfiguracja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="a44a9-178">Azure configuration</span></span>

* <span data-ttu-id="a44a9-179">Upewnij się, że rejestracja aplikacji *serwera* ma dostęp do interfejsu API interfejs API programu Graph uprawnienia dla programu `Directory.Read.All` , który jest poziomem dostępu o najniższych uprawnieniach dla grup zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a44a9-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="a44a9-180">Upewnij się, że zgoda administratora została zastosowana do uprawnienia po wprowadzeniu przypisania uprawnień.</span><span class="sxs-lookup"><span data-stu-id="a44a9-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="a44a9-181">Przypisz nowy wpis tajny klienta do aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="a44a9-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="a44a9-182">Zanotuj wpis tajny konfiguracji aplikacji w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="a44a9-183">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="a44a9-183">App settings</span></span>

<span data-ttu-id="a44a9-184">W pliku ustawień aplikacji ( `appsettings.json` lub `appsettings.Production.json` ) Utwórz `ClientSecret` wpis z użyciem klucza tajnego klienta aplikacji *serwera* z Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="a44a9-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="a44a9-185">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a44a9-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="a44a9-186">Zasady autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a44a9-186">Authorization policies</span></span>

<span data-ttu-id="a44a9-187">Utwórz [zasady autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń usługi AAD i ról administratora usługi AAD *Server* w aplikacji serwera `Startup.ConfigureServices` ( `Startup.cs` ) na podstawie identyfikatorów obiektów grup i [identyfikatorów obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="a44a9-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="a44a9-188">Na przykład zasady roli administrator rozliczeń platformy Azure mają następującą konfigurację:</span><span class="sxs-lookup"><span data-stu-id="a44a9-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a44a9-189">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="a44a9-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="a44a9-190">Dostęp do kontrolera</span><span class="sxs-lookup"><span data-stu-id="a44a9-190">Controller access</span></span>

<span data-ttu-id="a44a9-191">Wymagaj zasad na kontrolerach aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="a44a9-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="a44a9-192">Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdmin` , zgodnie z konfiguracją w sekcji [zasady autoryzacji](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="a44a9-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="a44a9-193">Konfiguracja usługi</span><span class="sxs-lookup"><span data-stu-id="a44a9-193">Service configuration</span></span>

<span data-ttu-id="a44a9-194">W metodzie aplikacji *serwera* `Startup.ConfigureServices` Dodaj logikę, aby interfejs API programu Graph wywołać i ustanowić oświadczenia użytkowników `group` dla grup zabezpieczeń i ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a44a9-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="a44a9-195">Przykładowy kod w tej sekcji używa Active Directory Authentication Library (ADAL), który jest oparty na Identity platformie Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="a44a9-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="a44a9-196">Ten temat zostanie zaktualizowany w Identity wersji 2.0 dla programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="a44a9-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="a44a9-197">Śledź postęp tej pracy przez monitorowanie [[RC1] Microsoft Identity platform 2,0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span><span class="sxs-lookup"><span data-stu-id="a44a9-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="a44a9-198">Dodatkowe przestrzenie nazw są wymagane dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="a44a9-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a44a9-199">Poniższy zestaw `using` instrukcji zawiera przestrzenie nazw wymagane dla kodu, który następuje po tej sekcji:</span><span class="sxs-lookup"><span data-stu-id="a44a9-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="a44a9-200">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="a44a9-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a44a9-201">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a44a9-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a44a9-202">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="a44a9-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a44a9-203">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a44a9-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a44a9-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="a44a9-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a44a9-205">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="a44a9-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a44a9-206">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a44a9-206">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a44a9-207">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a44a9-207">In the preceding example:</span></span>

* <span data-ttu-id="a44a9-208">Najpierw podjęto próbę pozyskania tokenu dyskretnego, <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ponieważ token dostępu mógł już zostać zapisany w pamięci podręcznej tokenów biblioteki ADAL.</span><span class="sxs-lookup"><span data-stu-id="a44a9-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="a44a9-209">Pobieranie tokenu z pamięci podręcznej jest szybsze niż żądanie nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="a44a9-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="a44a9-210">Jeśli token dostępu nie zostanie uzyskany z pamięci podręcznej ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> lub <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> jest zgłaszany), potwierdzenie użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) zostanie wykonane przy użyciu poświadczeń klienta ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) w celu uzyskania tokenu w imieniu użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="a44a9-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="a44a9-211">Następnie można dalej `Microsoft.Graph.GraphServiceClient` używać tokenu, aby wywołać interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="a44a9-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="a44a9-212">Token jest umieszczany w pamięci podręcznej tokenów ADAL.</span><span class="sxs-lookup"><span data-stu-id="a44a9-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="a44a9-213">W przypadku przyszłych interfejs API programu Graph wywołań dla tego samego użytkownika token jest uzyskiwany z pamięci podręcznej dyskretnie z <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="a44a9-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="a44a9-214">Kod w <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie uzyskuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="a44a9-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="a44a9-215">Aby zmienić kod w celu uzyskania bezpośrednich i przechodnich członkostw:</span><span class="sxs-lookup"><span data-stu-id="a44a9-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="a44a9-216">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="a44a9-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="a44a9-217">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="a44a9-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="a44a9-218">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="a44a9-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="a44a9-219">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="a44a9-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="a44a9-220">Kod w programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie rozróżnia grup zabezpieczeń usługi AAD i ról administratorów usługi AAD podczas tworzenia oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="a44a9-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="a44a9-221">Aby można było rozróżnić grupę i role w aplikacji, należy sprawdzić `entry.ODataType` podczas iterowania przez grupy i role.</span><span class="sxs-lookup"><span data-stu-id="a44a9-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="a44a9-222">Aby utworzyć oddzielną grupę zabezpieczeń i oświadczenia ról, należy użyć kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="a44a9-222">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="a44a9-223">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="a44a9-223">User-defined roles</span></span>

<span data-ttu-id="a44a9-224">Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a44a9-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="a44a9-225">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a44a9-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="a44a9-226">W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:</span><span class="sxs-lookup"><span data-stu-id="a44a9-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="a44a9-227">Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a44a9-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="a44a9-228">Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.</span><span class="sxs-lookup"><span data-stu-id="a44a9-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="a44a9-229">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="a44a9-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="a44a9-230">Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="a44a9-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="a44a9-231">Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="a44a9-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="a44a9-232">`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="a44a9-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="a44a9-233">Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania, jak pokazano w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-233">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="a44a9-234">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="a44a9-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="a44a9-235">W `Program.Main` autonomicznej aplikacji lub *`Client`* aplikacji rozwiązania hostowanego Blazor należy określić `role` rolę "" jako element Claim role:</span><span class="sxs-lookup"><span data-stu-id="a44a9-235">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="a44a9-236">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="a44a9-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a44a9-237">Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="a44a9-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="a44a9-238">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="a44a9-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a44a9-239">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="a44a9-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a44a9-240">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="a44a9-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a44a9-241">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="a44a9-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="a44a9-242">Identyfikatory obiektów roli administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="a44a9-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="a44a9-243">Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="a44a9-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="a44a9-244">Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a44a9-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="a44a9-245">Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i role administratorów usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="a44a9-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="a44a9-246">Rola administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="a44a9-246">AAD Administrator Role</span></span> | <span data-ttu-id="a44a9-247">Identyfikator obiektu</span><span class="sxs-lookup"><span data-stu-id="a44a9-247">Object ID</span></span>
--- | ---
<span data-ttu-id="a44a9-248">Administrator aplikacji</span><span class="sxs-lookup"><span data-stu-id="a44a9-248">Application administrator</span></span> | <span data-ttu-id="a44a9-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="a44a9-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="a44a9-250">Deweloper aplikacji</span><span class="sxs-lookup"><span data-stu-id="a44a9-250">Application developer</span></span> | <span data-ttu-id="a44a9-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="a44a9-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="a44a9-252">Administrator uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a44a9-252">Authentication administrator</span></span> | <span data-ttu-id="a44a9-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="a44a9-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="a44a9-254">Administrator usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="a44a9-254">Azure DevOps administrator</span></span> | <span data-ttu-id="a44a9-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="a44a9-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="a44a9-256">Azure Information Protection administrator</span><span class="sxs-lookup"><span data-stu-id="a44a9-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="a44a9-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="a44a9-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="a44a9-258">Administrator zestawu kluczy B2C IEF</span><span class="sxs-lookup"><span data-stu-id="a44a9-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="a44a9-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="a44a9-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="a44a9-260">Administrator zasad B2C IEF</span><span class="sxs-lookup"><span data-stu-id="a44a9-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="a44a9-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="a44a9-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="a44a9-262">Administrator przepływu użytkownika B2C</span><span class="sxs-lookup"><span data-stu-id="a44a9-262">B2C user flow administrator</span></span> | <span data-ttu-id="a44a9-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="a44a9-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="a44a9-264">B2C atrybutu przepływu użytkownika</span><span class="sxs-lookup"><span data-stu-id="a44a9-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="a44a9-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="a44a9-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="a44a9-266">Administrator rozliczeń</span><span class="sxs-lookup"><span data-stu-id="a44a9-266">Billing administrator</span></span> | <span data-ttu-id="a44a9-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="a44a9-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="a44a9-268">Administrator aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="a44a9-268">Cloud application administrator</span></span> | <span data-ttu-id="a44a9-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="a44a9-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="a44a9-270">Administrator urządzenia w chmurze</span><span class="sxs-lookup"><span data-stu-id="a44a9-270">Cloud device administrator</span></span> | <span data-ttu-id="a44a9-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="a44a9-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="a44a9-272">Administrator zgodności</span><span class="sxs-lookup"><span data-stu-id="a44a9-272">Compliance administrator</span></span> | <span data-ttu-id="a44a9-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="a44a9-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="a44a9-274">Administrator danych zgodności</span><span class="sxs-lookup"><span data-stu-id="a44a9-274">Compliance data administrator</span></span> | <span data-ttu-id="a44a9-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="a44a9-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="a44a9-276">Administrator dostępu warunkowego</span><span class="sxs-lookup"><span data-stu-id="a44a9-276">Conditional Access administrator</span></span> | <span data-ttu-id="a44a9-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="a44a9-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="a44a9-278">Osoba zatwierdzająca dostęp do skrytki klienta</span><span class="sxs-lookup"><span data-stu-id="a44a9-278">Customer LockBox access approver</span></span> | <span data-ttu-id="a44a9-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="a44a9-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="a44a9-280">Administrator usługi Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="a44a9-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="a44a9-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="a44a9-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="a44a9-282">Czytelnicy katalogów</span><span class="sxs-lookup"><span data-stu-id="a44a9-282">Directory readers</span></span> | <span data-ttu-id="a44a9-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="a44a9-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="a44a9-284">Administrator systemu Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="a44a9-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="a44a9-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="a44a9-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="a44a9-286">Administrator programu Exchange</span><span class="sxs-lookup"><span data-stu-id="a44a9-286">Exchange administrator</span></span> | <span data-ttu-id="a44a9-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="a44a9-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="a44a9-288">IdentityAdministrator dostawcy zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="a44a9-288">External Identity Provider administrator</span></span> | <span data-ttu-id="a44a9-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="a44a9-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="a44a9-290">Administrator globalny</span><span class="sxs-lookup"><span data-stu-id="a44a9-290">Global administrator</span></span> | <span data-ttu-id="a44a9-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="a44a9-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="a44a9-292">Czytnik globalny</span><span class="sxs-lookup"><span data-stu-id="a44a9-292">Global reader</span></span> | <span data-ttu-id="a44a9-293">f6903b21-6aba-4124-B44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="a44a9-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="a44a9-294">Administrator grup</span><span class="sxs-lookup"><span data-stu-id="a44a9-294">Groups administrator</span></span> | <span data-ttu-id="a44a9-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="a44a9-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="a44a9-296">Zapraszający gościa</span><span class="sxs-lookup"><span data-stu-id="a44a9-296">Guest inviter</span></span> | <span data-ttu-id="a44a9-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="a44a9-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="a44a9-298">Administrator pomocy technicznej</span><span class="sxs-lookup"><span data-stu-id="a44a9-298">Helpdesk administrator</span></span> | <span data-ttu-id="a44a9-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="a44a9-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="a44a9-300">Administrator usługi Intune</span><span class="sxs-lookup"><span data-stu-id="a44a9-300">Intune administrator</span></span> | <span data-ttu-id="a44a9-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="a44a9-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="a44a9-302">Usługi kaizala administrator</span><span class="sxs-lookup"><span data-stu-id="a44a9-302">Kaizala administrator</span></span> | <span data-ttu-id="a44a9-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="a44a9-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="a44a9-304">Administrator licencji</span><span class="sxs-lookup"><span data-stu-id="a44a9-304">License administrator</span></span> | <span data-ttu-id="a44a9-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="a44a9-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="a44a9-306">Czytnik prywatności centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="a44a9-306">Message center privacy reader</span></span> | <span data-ttu-id="a44a9-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="a44a9-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="a44a9-308">Czytelnik centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="a44a9-308">Message center reader</span></span> | <span data-ttu-id="a44a9-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="a44a9-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="a44a9-310">Administrator aplikacji pakietu Office</span><span class="sxs-lookup"><span data-stu-id="a44a9-310">Office apps administrator</span></span> | <span data-ttu-id="a44a9-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="a44a9-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="a44a9-312">Administrator haseł</span><span class="sxs-lookup"><span data-stu-id="a44a9-312">Password administrator</span></span> | <span data-ttu-id="a44a9-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="a44a9-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="a44a9-314">Power BI administrator</span><span class="sxs-lookup"><span data-stu-id="a44a9-314">Power BI administrator</span></span> | <span data-ttu-id="a44a9-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="a44a9-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="a44a9-316">platforma Power administrator</span><span class="sxs-lookup"><span data-stu-id="a44a9-316">Power platform administrator</span></span> | <span data-ttu-id="a44a9-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="a44a9-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="a44a9-318">Administrator uprzywilejowanego uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a44a9-318">Privileged authentication administrator</span></span> | <span data-ttu-id="a44a9-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="a44a9-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="a44a9-320">Administrator ról uprzywilejowanych</span><span class="sxs-lookup"><span data-stu-id="a44a9-320">Privileged role administrator</span></span> | <span data-ttu-id="a44a9-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="a44a9-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="a44a9-322">Czytelnik raportów</span><span class="sxs-lookup"><span data-stu-id="a44a9-322">Reports reader</span></span> | <span data-ttu-id="a44a9-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="a44a9-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="a44a9-324">Administrator wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="a44a9-324">Search administrator</span></span> | <span data-ttu-id="a44a9-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="a44a9-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="a44a9-326">Edytor wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="a44a9-326">Search editor</span></span> | <span data-ttu-id="a44a9-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="a44a9-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="a44a9-328">Administrator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="a44a9-328">Security administrator</span></span> | <span data-ttu-id="a44a9-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="a44a9-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="a44a9-330">Operator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="a44a9-330">Security operator</span></span> | <span data-ttu-id="a44a9-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="a44a9-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="a44a9-332">Czytelnik zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="a44a9-332">Security reader</span></span> | <span data-ttu-id="a44a9-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="a44a9-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="a44a9-334">Administrator pomocy technicznej usługi</span><span class="sxs-lookup"><span data-stu-id="a44a9-334">Service support administrator</span></span> | <span data-ttu-id="a44a9-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="a44a9-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="a44a9-336">Administrator programu SharePoint</span><span class="sxs-lookup"><span data-stu-id="a44a9-336">SharePoint administrator</span></span> | <span data-ttu-id="a44a9-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="a44a9-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="a44a9-338">Administrator programu Skype dla firm</span><span class="sxs-lookup"><span data-stu-id="a44a9-338">Skype for Business administrator</span></span> | <span data-ttu-id="a44a9-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="a44a9-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="a44a9-340">Administratorzy zespołu ds. komunikacji</span><span class="sxs-lookup"><span data-stu-id="a44a9-340">Teams Communications Administrator</span></span> | <span data-ttu-id="a44a9-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="a44a9-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="a44a9-342">Inżynierowie pomocy technicznej dla zespołów</span><span class="sxs-lookup"><span data-stu-id="a44a9-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="a44a9-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="a44a9-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="a44a9-344">Zespoły komunikacyjne specjalisty</span><span class="sxs-lookup"><span data-stu-id="a44a9-344">Teams Communications Specialist</span></span> | <span data-ttu-id="a44a9-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="a44a9-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="a44a9-346">Administrator usługi Teams</span><span class="sxs-lookup"><span data-stu-id="a44a9-346">Teams Service Administrator</span></span> | <span data-ttu-id="a44a9-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="a44a9-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="a44a9-348">Administrator użytkowników</span><span class="sxs-lookup"><span data-stu-id="a44a9-348">User administrator</span></span> | <span data-ttu-id="a44a9-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="a44a9-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a44a9-350">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a44a9-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
