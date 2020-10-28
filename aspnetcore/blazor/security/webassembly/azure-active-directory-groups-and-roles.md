---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: z grupami Azure Active Directory i rolami'
author: guardrex
description: 'Dowiedz się, jak skonfigurować :::no-loc(Blazor WebAssembly)::: program, aby używać grup i ról Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a56f03f2c3acd08b009673ef7533186bf703604
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690463"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="0e0d7-103">Grupy Azure Active Directory (AAD), role administratorów i role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0e0d7-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="0e0d7-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0e0d7-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="0e0d7-105">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z :::no-loc(ASP.NET Core Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="0e0d7-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="0e0d7-106">Grupy zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0e0d7-106">User-defined groups</span></span>
  * <span data-ttu-id="0e0d7-107">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="0e0d7-107">Security</span></span>
  * <span data-ttu-id="0e0d7-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="0e0d7-108">Microsoft 365</span></span>
  * <span data-ttu-id="0e0d7-109">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="0e0d7-109">Distribution</span></span>
* <span data-ttu-id="0e0d7-110">Role</span><span class="sxs-lookup"><span data-stu-id="0e0d7-110">Roles</span></span>
  * <span data-ttu-id="0e0d7-111">Role administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="0e0d7-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="0e0d7-112">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0e0d7-112">User-defined roles</span></span>

<span data-ttu-id="0e0d7-113">Wskazówki zawarte w tym artykule dotyczą :::no-loc(Blazor WebAssembly)::: scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-113">The guidance in this article applies to the :::no-loc(Blazor WebAssembly)::: AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="0e0d7-114">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="0e0d7-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="0e0d7-115">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="0e0d7-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="0e0d7-116">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="0e0d7-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="0e0d7-117">Zakresy</span><span class="sxs-lookup"><span data-stu-id="0e0d7-117">Scopes</span></span>

<span data-ttu-id="0e0d7-118">Wywołanie [interfejsu API Microsoft Graph](/graph/use-the-api) jest wymagane dla wszystkich użytkowników aplikacji mających więcej niż pięć ról administratora usługi AAD i członkostwa w grupach zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="0e0d7-119">Aby zezwolić na interfejs API programu Graph wywołań, nadaje autonomiczną lub *`Client`* aplikację hostowanego :::no-loc(Blazor)::: rozwiązania dowolne z następujących [uprawnień interfejs API programu Graph (zakresy)](/graph/permissions-reference) w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted :::no-loc(Blazor)::: solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="0e0d7-120">`Directory.Read.All` jest zakresem najniższych uprawnień i jest zakresem używanym dla przykładu opisanego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-120">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="0e0d7-121">Grupy zdefiniowane przez użytkownika i role administratorów</span><span class="sxs-lookup"><span data-stu-id="0e0d7-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="0e0d7-122">Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="0e0d7-123">Przypisywanie użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i ról administratorów usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="0e0d7-124">Role korzystające z grup zabezpieczeń usługi Azure AD</span><span class="sxs-lookup"><span data-stu-id="0e0d7-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="0e0d7-125">`groupMembershipClaims` przypisane</span><span class="sxs-lookup"><span data-stu-id="0e0d7-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="0e0d7-126">W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="0e0d7-127">Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="0e0d7-128">Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="0e0d7-129">Gdy liczba przypisanych ról administratora usługi AAD i grup zdefiniowanych przez użytkownika przekracza pięć, w usłudze AAD zostanie wysłane zgłoszenie do `hasgroups` `true` wartości zamiast wysyłania `groups` żądania.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="0e0d7-130">Wszystkie aplikacje, które mogą mieć więcej niż pięć ról i grup przypisanych do swoich użytkowników, muszą wykonać oddzielne wywołanie interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="0e0d7-131">Przykładowa implementacja podana w tym artykule dotyczy tego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="0e0d7-132">Aby uzyskać więcej informacji, zobacz `groups` `hasgroups` artykuł i informacje o oświadczeniach w [tokenach dostępu do platformy tożsamości firmy Microsoft: oświadczenie dotyczące ładunku](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="0e0d7-133"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="0e0d7-134">Przypisz pustą tablicę do każdej właściwości, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach później.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="0e0d7-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-135">`CustomUserAccount.cs`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0e0d7-136">Użyj **jednego** z poniższych metod, aby utworzyć oświadczenia dla grup i ról usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-136">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="0e0d7-137">Korzystanie z zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="0e0d7-137">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="0e0d7-138">Użyj nazwanego `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="0e0d7-138">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="0e0d7-139">Korzystanie z zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="0e0d7-139">Use the Graph SDK</span></span>

<span data-ttu-id="0e0d7-140">Dodaj odwołanie do pakietu do aplikacji autonomicznej lub *`Client`* aplikacji hostowanego :::no-loc(Blazor)::: rozwiązania dla programu [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-140">Add a package reference to the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="0e0d7-141">Dodaj klasy i konfigurację narzędzia zestawu Graph SDK w sekcji *zestaw Graph SDK* <xref:blazor/security/webassembly/graph-api#graph-sdk> artykułu.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-141">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="0e0d7-142">Dodaj następującą niestandardową fabrykę kont użytkowników do autonomicznej appo lub *`Client`* aplikacji hostowanego :::no-loc(Blazor)::: rozwiązania ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="0e0d7-142">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted :::no-loc(Blazor)::: solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="0e0d7-143">Niestandardowa fabryka użytkowników służy do przetwarzania oświadczeń ról i grup.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-143">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="0e0d7-144">`roles`Tablica roszczeń znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-144">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="0e0d7-145">Jeśli `hasgroups` jest obecny, zestaw Graph SDK jest używany do autoryzowania żądania interfejs API programu Graph w celu uzyskania ról i grup użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-145">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = user:::no-loc(Identity):::.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                user:::no-loc(Identity):::.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="0e0d7-146">Poprzedzający kod nie obejmuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-146">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="0e0d7-147">Jeśli aplikacja wymaga bezpośrednich i przechodnich oświadczeń członkostwa w grupie:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-147">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="0e0d7-148">Zmień `IUserMemberOfCollectionWithReferencesPage` Typ na `groupsAndAzureRoles` na `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-148">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="0e0d7-149">Podczas żądania grup i ról użytkownika Zastąp `MemberOf` Właściwość opcją `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-149">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="0e0d7-150">W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-150">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="0e0d7-151">Użyj nazwanego `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="0e0d7-151">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="0e0d7-152">W aplikacji autonomicznej lub *`Client`* aplikacji rozwiązania hostowanego :::no-loc(Blazor)::: Utwórz klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-152">In the standalone app or the *`Client`* app of a hosted :::no-loc(Blazor)::: solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="0e0d7-153">Użyj poprawnego zakresu dla wywołań interfejs API programu Graph, które uzyskują informacje o rolach i grupach.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-153">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="0e0d7-154">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-154">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="0e0d7-155">W programie `Program.Main` ( `Program.cs` ) Dodaj <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> usługę implementacji i Dodaj nazwę <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-155">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="0e0d7-156">Poniższy przykład nazywa klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="0e0d7-156">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="0e0d7-157">Utwórz klasy obiektów katalogu usługi AAD, aby otrzymywać role i grupy protokołu Open Data Protocol (OData) z wywołania interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-157">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="0e0d7-158">Dane OData docierają do formatu JSON i wywołanie do <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> wypełnienia wystąpienia `DirectoryObjects` klasy.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-158">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="0e0d7-159">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-159">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="0e0d7-160">Utwórz niestandardową fabrykę użytkowników, aby przetwarzać oświadczenia ról i grup.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-160">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="0e0d7-161">Poniższa przykładowa implementacja obsługuje również `roles` tablicę roszczeń, która znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-161">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="0e0d7-162">Jeśli `hasgroups` jest obecny wniosek, nazwa <xref:System.Net.Http.HttpClient> jest używana do autoryzowania żądania interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-162">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="0e0d7-163">W tej implementacji jest stosowany :::no-loc(Identity)::: punkt końcowy platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([Dokumentacja interfejsu API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="0e0d7-163">This implementation uses the Microsoft :::no-loc(Identity)::: Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="0e0d7-164">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-164">`CustomAccountFactory.cs`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
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
                            user:::no-loc(Identity):::.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        user:::no-loc(Identity):::.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="0e0d7-165">Nie trzeba podawać kodu, aby usunąć pierwotne `groups` zastrzeżenie, jeśli jest obecny, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-165">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="0e0d7-166">Podejście w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-166">The approach in this example:</span></span>
>
> * <span data-ttu-id="0e0d7-167">Dodaje klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , aby dołączyć tokeny dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-167">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="0e0d7-168">Dodaje nazwane <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejsu API sieci Web do bezpiecznego, zewnętrznego punktu końcowego interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-168">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="0e0d7-169">Używa nazwy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-169">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="0e0d7-170">Ogólny zakres tego podejścia znajduje się w <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artykule.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-170">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="0e0d7-171">Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) autonomicznej aplikacji lub *`Client`* aplikacji hostowanego :::no-loc(Blazor)::: rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-171">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution.</span></span> <span data-ttu-id="0e0d7-172">Wyrażanie zgody na `Directory.Read.All` zakres jako dodatkowy zakres aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-172">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

## <a name="authorization-configuration"></a><span data-ttu-id="0e0d7-173">Konfiguracja autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-173">Authorization configuration</span></span>

<span data-ttu-id="0e0d7-174">Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-174">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="0e0d7-175">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-175">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="0e0d7-176">Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-176">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="0e0d7-177">W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-177">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="0e0d7-178">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-178">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="0e0d7-179">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="0e0d7-179">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="0e0d7-180">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-180">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="0e0d7-181">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="0e0d7-181">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="0e0d7-182">Autoryzuj dostęp do interfejsu API serwera dla grup zdefiniowanych przez użytkownika i ról administratorów</span><span class="sxs-lookup"><span data-stu-id="0e0d7-182">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="0e0d7-183">Oprócz autoryzowania użytkowników w aplikacji webassembly po stronie klienta w celu uzyskania dostępu do stron i zasobów, interfejs API serwera może autoryzować użytkowników w celu uzyskania dostępu do bezpiecznych punktów końcowych interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-183">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="0e0d7-184">Gdy aplikacja *serwera* zweryfikuje token dostępu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-184">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="0e0d7-185">Aplikacja interfejsu API serwera używa niezmiennego [identyfikatora obiektu użytkownika ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) z tokenu dostępu, aby uzyskać token dostępu dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-185">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="0e0d7-186">Wywołanie interfejs API programu Graph uzyskuje członkostwo użytkownika zdefiniowanego przez użytkownika na platformie Azure i grupy zabezpieczeń, wywołując użytkownika [`memberOf`](/graph/api/user-list-memberof) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-186">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="0e0d7-187">Członkostwa są używane do ustanawiania `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-187">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="0e0d7-188">[Zasady autoryzacji](xref:security/authorization/policies) mogą służyć do ograniczania dostępu użytkowników do punktów końcowych interfejsu API serwera w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-188">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0e0d7-189">Te wskazówki nie obejmują obecnie autoryzacji użytkowników na podstawie [ról zdefiniowanych przez użytkownika usługi AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="0e0d7-189">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="0e0d7-190">Wskazówki zawarte w tej sekcji konfigurują aplikację interfejsu API serwera jako [*aplikację demona*](/azure/active-directory/develop/scenario-daemon-overview) dla wywołania interfejsu API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-190">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="0e0d7-191">Takie podejście **nie** jest następujące:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-191">This approach does **not** :</span></span>

* <span data-ttu-id="0e0d7-192">Wymagaj `access_as_user` zakresu.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-192">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="0e0d7-193">Interfejs API programu Graph dostępu w imieniu użytkownika/klienta wykonującego żądanie interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-193">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="0e0d7-194">Wywołanie interfejs API programu Graph **przez aplikację interfejsu API serwera interfejs API programu Graph wymaga** tylko, aby `Directory.Read.All` w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-194">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="0e0d7-195">Takie podejście bezproblemowo uniemożliwia aplikacji klienckiej uzyskanie dostępu do danych katalogu, których interfejs API serwera nie zezwoli jawnie.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-195">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="0e0d7-196">Aplikacja kliencka może uzyskać dostęp tylko do punktów końcowych kontrolera aplikacji interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-196">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="0e0d7-197">Konfiguracja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="0e0d7-197">Azure configuration</span></span>

* <span data-ttu-id="0e0d7-198">Upewnij się, że rejestracja aplikacji *serwera* ma przydaną **aplikację** (nie **delegowaną** ) interfejs API programu Graph zakresem `Directory.Read.All` , który jest poziomem dostępu o najniższych uprawnieniach dla grup zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-198">Confirm that the *Server* app registration is given **Application** (not **Delegated** ) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="0e0d7-199">Upewnij się, że do zakresu zastosowana jest zgoda administratora, po wprowadzeniu przypisania zakresu.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-199">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="0e0d7-200">Przypisz nowy wpis tajny klienta do aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-200">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="0e0d7-201">Zanotuj wpis tajny konfiguracji aplikacji w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-201">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="0e0d7-202">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-202">App settings</span></span>

<span data-ttu-id="0e0d7-203">W pliku ustawień aplikacji ( `appsettings.json` lub `appsettings.Production.json` ) Utwórz `ClientSecret` wpis z użyciem klucza tajnego klienta aplikacji *serwera* z Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-203">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="0e0d7-204">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-204">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0e0d7-205">Jeśli domena wydawcy dzierżawcy nie została zweryfikowana, zakres interfejsu API serwera dla dostępu użytkownika/klienta korzysta z `https://` identyfikatora URI opartego na identyfikatorze.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-205">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="0e0d7-206">W tym scenariuszu aplikacja interfejsu API serwera wymaga `Audience` konfiguracji w `appsettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-206">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="0e0d7-207">W poniższej konfiguracji koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/{DEFAULT SCOPE}` , gdzie symbol zastępczy `{DEFAULT SCOPE}` jest zakresem domyślnym:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-207">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="0e0d7-208">W powyższej przykładowej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-208">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="0e0d7-209">Domena dzierżawy to `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-209">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="0e0d7-210">Aplikacja interfejsu API serwera `ClientId` to `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-210">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="0e0d7-211">Konfigurowanie `Audience` jawnie zwykle **nie** jest wymagane w przypadku aplikacji z zweryfikowaną domeną wydawcy, która ma `api://` zakres interfejsu API opartego na systemie.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-211">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="0e0d7-212">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-212">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="0e0d7-213">Zasady autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-213">Authorization policies</span></span>

<span data-ttu-id="0e0d7-214">Utwórz [zasady autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń usługi AAD i ról administratora usługi AAD *Server* w aplikacji serwera `Startup.ConfigureServices` ( `Startup.cs` ) na podstawie identyfikatorów obiektów grup i [identyfikatorów obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="0e0d7-214">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="0e0d7-215">Na przykład zasady roli administrator rozliczeń platformy Azure mają następującą konfigurację:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-215">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="0e0d7-216">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-216">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="0e0d7-217">Dostęp do kontrolera</span><span class="sxs-lookup"><span data-stu-id="0e0d7-217">Controller access</span></span>

<span data-ttu-id="0e0d7-218">Wymagaj zasad na kontrolerach aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-218">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="0e0d7-219">Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdmin` , zgodnie z konfiguracją w sekcji [zasady autoryzacji](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="0e0d7-219">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="0e0d7-220">Pakiety</span><span class="sxs-lookup"><span data-stu-id="0e0d7-220">Packages</span></span>

<span data-ttu-id="0e0d7-221">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-221">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="0e0d7-222">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="0e0d7-222">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="0e0d7-223">[Microsoft. :::no-loc(Identity)::: . Klient](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span><span class="sxs-lookup"><span data-stu-id="0e0d7-223">[Microsoft.:::no-loc(Identity):::.Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span></span>

### <a name="services"></a><span data-ttu-id="0e0d7-224">Usługi</span><span class="sxs-lookup"><span data-stu-id="0e0d7-224">Services</span></span>

<span data-ttu-id="0e0d7-225">W metodzie aplikacji *serwera* `Startup.ConfigureServices` są wymagane dodatkowe przestrzenie nazw dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-225">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="0e0d7-226">Dodaj następujące przestrzenie nazw do `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="0e0d7-226">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.:::no-loc(Identity):::.Client;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="0e0d7-227">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="0e0d7-227">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="0e0d7-228">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-228">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0e0d7-229">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-229">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="0e0d7-230">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-230">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="0e0d7-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="0e0d7-232">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-232">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="0e0d7-233">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-233">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="0e0d7-234">W poprzednim kodzie Obsługa następujących błędów tokenu jest opcjonalna:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-234">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="0e0d7-235">`MsalUiRequiredException`: Aplikacja nie ma wystarczających uprawnień (zakresów).</span><span class="sxs-lookup"><span data-stu-id="0e0d7-235">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="0e0d7-236">Ustal, czy zakresy aplikacji interfejsu API serwera w Azure Portal zawierają uprawnienia **aplikacji** dla programu `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-236">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="0e0d7-237">Upewnij się, że administrator dzierżawy udzielił uprawnień do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-237">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="0e0d7-238">`MsalServiceException` ( `AADSTS70011` ): Upewnij się, że zakres to `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-238">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="0e0d7-239">Pakiety</span><span class="sxs-lookup"><span data-stu-id="0e0d7-239">Packages</span></span>

<span data-ttu-id="0e0d7-240">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-240">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="0e0d7-241">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="0e0d7-241">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="0e0d7-242">[Firma Microsoft. :::no-loc(Identity)::: Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="0e0d7-242">[Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="0e0d7-243">Konfiguracja usługi</span><span class="sxs-lookup"><span data-stu-id="0e0d7-243">Service configuration</span></span>

<span data-ttu-id="0e0d7-244">W metodzie aplikacji *serwera* `Startup.ConfigureServices` Dodaj logikę, aby interfejs API programu Graph wywołać i ustanowić oświadczenia użytkowników `group` dla grup zabezpieczeń i ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-244">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="0e0d7-245">Przykładowy kod w tej sekcji używa Active Directory Authentication Library (ADAL), który jest oparty na :::no-loc(Identity)::: platformie Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-245">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft :::no-loc(Identity)::: Platform v1.0.</span></span>

<span data-ttu-id="0e0d7-246">Dodatkowe przestrzenie nazw są wymagane dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-246">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="0e0d7-247">Poniższy zestaw `using` instrukcji zawiera przestrzenie nazw wymagane dla kodu, który następuje po tej sekcji:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-247">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
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
using Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="0e0d7-248">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="0e0d7-248">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="0e0d7-249">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-249">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0e0d7-250">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-250">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="0e0d7-251">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-251">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="0e0d7-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="0e0d7-253">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-253">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="0e0d7-254">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-254">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
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

                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

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
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="0e0d7-255">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-255">In the preceding example:</span></span>

* <span data-ttu-id="0e0d7-256">Najpierw podjęto próbę pozyskania tokenu dyskretnego, <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ponieważ token dostępu mógł już zostać zapisany w pamięci podręcznej tokenów biblioteki ADAL.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-256">Silent token acquisition (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="0e0d7-257">Pobieranie tokenu z pamięci podręcznej jest szybsze niż żądanie nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-257">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="0e0d7-258">Jeśli token dostępu nie zostanie uzyskany z pamięci podręcznej ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> lub <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> jest zgłaszany), potwierdzenie użytkownika ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion> ) zostanie wykonane przy użyciu poświadczeń klienta ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential> ) w celu uzyskania tokenu w imieniu użytkownika ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="0e0d7-258">If the access token isn't acquired from cache (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="0e0d7-259">Następnie można dalej `Microsoft.Graph.GraphServiceClient` używać tokenu, aby wywołać interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-259">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="0e0d7-260">Token jest umieszczany w pamięci podręcznej tokenów ADAL.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-260">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="0e0d7-261">W przypadku przyszłych interfejs API programu Graph wywołań dla tego samego użytkownika token jest uzyskiwany z pamięci podręcznej dyskretnie z <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-261">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="0e0d7-262">Kod w <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie uzyskuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-262">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="0e0d7-263">Aby zmienić kod w celu uzyskania bezpośrednich i przechodnich członkostw:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-263">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="0e0d7-264">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-264">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="0e0d7-265">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-265">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="0e0d7-266">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-266">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="0e0d7-267">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-267">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="0e0d7-268">Kod w programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie rozróżnia grup zabezpieczeń usługi AAD i ról administratorów usługi AAD podczas tworzenia oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="0e0d7-269">Aby można było rozróżnić grupę i role w aplikacji, należy sprawdzić `entry.ODataType` podczas iterowania przez grupy i role.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-269">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="0e0d7-270">Aby utworzyć oddzielną grupę zabezpieczeń i oświadczenia ról, należy użyć kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-270">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        user:::no-loc(Identity):::.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="0e0d7-271">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="0e0d7-271">User-defined roles</span></span>

<span data-ttu-id="0e0d7-272">Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-272">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="0e0d7-273">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-273">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="0e0d7-274">W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-274">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="0e0d7-275">Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-275">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="0e0d7-276">Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-276">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="0e0d7-277">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-277">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="0e0d7-278">Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-278">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="0e0d7-279">Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-279">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="0e0d7-280">`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-280">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="0e0d7-281">Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub *`Client`* aplikacji hostowanego :::no-loc(Blazor)::: rozwiązania, jak pokazano w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-281">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="0e0d7-282">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-282">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="0e0d7-283">W `Program.Main` autonomicznej aplikacji lub *`Client`* aplikacji rozwiązania hostowanego :::no-loc(Blazor)::: należy określić `role` rolę "" jako element Claim role:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-283">In `Program.Main` of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="0e0d7-284">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-284">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="0e0d7-285">Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-285">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="0e0d7-286">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="0e0d7-286">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="0e0d7-287">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="0e0d7-287">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="0e0d7-288">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="0e0d7-288">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="0e0d7-289">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="0e0d7-289">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="0e0d7-290">Identyfikatory obiektów roli administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="0e0d7-290">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="0e0d7-291">Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-291">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="0e0d7-292">Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0e0d7-292">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="0e0d7-293">Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i role administratorów usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="0e0d7-293">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="0e0d7-294">Rola administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="0e0d7-294">AAD Administrator Role</span></span> | <span data-ttu-id="0e0d7-295">Identyfikator obiektu</span><span class="sxs-lookup"><span data-stu-id="0e0d7-295">Object ID</span></span>
--- | ---
<span data-ttu-id="0e0d7-296">Administrator aplikacji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-296">Application administrator</span></span> | <span data-ttu-id="0e0d7-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="0e0d7-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="0e0d7-298">Deweloper aplikacji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-298">Application developer</span></span> | <span data-ttu-id="0e0d7-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="0e0d7-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="0e0d7-300">Administrator uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0e0d7-300">Authentication administrator</span></span> | <span data-ttu-id="0e0d7-301">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="0e0d7-301">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="0e0d7-302">Administrator usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="0e0d7-302">Azure DevOps administrator</span></span> | <span data-ttu-id="0e0d7-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="0e0d7-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="0e0d7-304">Azure Information Protection administrator</span><span class="sxs-lookup"><span data-stu-id="0e0d7-304">Azure Information Protection administrator</span></span> | <span data-ttu-id="0e0d7-305">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="0e0d7-305">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="0e0d7-306">Administrator zestawu kluczy B2C IEF</span><span class="sxs-lookup"><span data-stu-id="0e0d7-306">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="0e0d7-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="0e0d7-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="0e0d7-308">Administrator zasad B2C IEF</span><span class="sxs-lookup"><span data-stu-id="0e0d7-308">B2C IEF Policy administrator</span></span> | <span data-ttu-id="0e0d7-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="0e0d7-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="0e0d7-310">Administrator przepływu użytkownika B2C</span><span class="sxs-lookup"><span data-stu-id="0e0d7-310">B2C user flow administrator</span></span> | <span data-ttu-id="0e0d7-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="0e0d7-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="0e0d7-312">B2C atrybutu przepływu użytkownika</span><span class="sxs-lookup"><span data-stu-id="0e0d7-312">B2C user flow attribute administrator</span></span> | <span data-ttu-id="0e0d7-313">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="0e0d7-313">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="0e0d7-314">Administrator rozliczeń</span><span class="sxs-lookup"><span data-stu-id="0e0d7-314">Billing administrator</span></span> | <span data-ttu-id="0e0d7-315">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="0e0d7-315">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="0e0d7-316">Administrator aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="0e0d7-316">Cloud application administrator</span></span> | <span data-ttu-id="0e0d7-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="0e0d7-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="0e0d7-318">Administrator urządzenia w chmurze</span><span class="sxs-lookup"><span data-stu-id="0e0d7-318">Cloud device administrator</span></span> | <span data-ttu-id="0e0d7-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="0e0d7-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="0e0d7-320">Administrator zgodności</span><span class="sxs-lookup"><span data-stu-id="0e0d7-320">Compliance administrator</span></span> | <span data-ttu-id="0e0d7-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="0e0d7-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="0e0d7-322">Administrator danych zgodności</span><span class="sxs-lookup"><span data-stu-id="0e0d7-322">Compliance data administrator</span></span> | <span data-ttu-id="0e0d7-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="0e0d7-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="0e0d7-324">Administrator dostępu warunkowego</span><span class="sxs-lookup"><span data-stu-id="0e0d7-324">Conditional Access administrator</span></span> | <span data-ttu-id="0e0d7-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="0e0d7-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="0e0d7-326">Osoba zatwierdzająca dostęp do skrytki klienta</span><span class="sxs-lookup"><span data-stu-id="0e0d7-326">Customer LockBox access approver</span></span> | <span data-ttu-id="0e0d7-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="0e0d7-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="0e0d7-328">Administrator usługi Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="0e0d7-328">Desktop Analytics administrator</span></span> | <span data-ttu-id="0e0d7-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="0e0d7-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="0e0d7-330">Czytelnicy katalogów</span><span class="sxs-lookup"><span data-stu-id="0e0d7-330">Directory readers</span></span> | <span data-ttu-id="0e0d7-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="0e0d7-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="0e0d7-332">Administrator systemu Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="0e0d7-332">Dynamics 365 administrator</span></span> | <span data-ttu-id="0e0d7-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="0e0d7-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="0e0d7-334">Administrator programu Exchange</span><span class="sxs-lookup"><span data-stu-id="0e0d7-334">Exchange administrator</span></span> | <span data-ttu-id="0e0d7-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="0e0d7-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="0e0d7-336">:::no-loc(Identity):::Administrator dostawcy zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="0e0d7-336">External :::no-loc(Identity)::: Provider administrator</span></span> | <span data-ttu-id="0e0d7-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="0e0d7-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="0e0d7-338">Administrator globalny</span><span class="sxs-lookup"><span data-stu-id="0e0d7-338">Global administrator</span></span> | <span data-ttu-id="0e0d7-339">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="0e0d7-339">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="0e0d7-340">Czytnik globalny</span><span class="sxs-lookup"><span data-stu-id="0e0d7-340">Global reader</span></span> | <span data-ttu-id="0e0d7-341">f6903b21-6aba-4124-B44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="0e0d7-341">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="0e0d7-342">Administrator grup</span><span class="sxs-lookup"><span data-stu-id="0e0d7-342">Groups administrator</span></span> | <span data-ttu-id="0e0d7-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="0e0d7-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="0e0d7-344">Zapraszający gościa</span><span class="sxs-lookup"><span data-stu-id="0e0d7-344">Guest inviter</span></span> | <span data-ttu-id="0e0d7-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="0e0d7-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="0e0d7-346">Administrator pomocy technicznej</span><span class="sxs-lookup"><span data-stu-id="0e0d7-346">Helpdesk administrator</span></span> | <span data-ttu-id="0e0d7-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="0e0d7-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="0e0d7-348">Administrator usługi Intune</span><span class="sxs-lookup"><span data-stu-id="0e0d7-348">Intune administrator</span></span> | <span data-ttu-id="0e0d7-349">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="0e0d7-349">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="0e0d7-350">Usługi kaizala administrator</span><span class="sxs-lookup"><span data-stu-id="0e0d7-350">Kaizala administrator</span></span> | <span data-ttu-id="0e0d7-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="0e0d7-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="0e0d7-352">Administrator licencji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-352">License administrator</span></span> | <span data-ttu-id="0e0d7-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="0e0d7-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="0e0d7-354">Czytnik prywatności centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="0e0d7-354">Message center privacy reader</span></span> | <span data-ttu-id="0e0d7-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="0e0d7-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="0e0d7-356">Czytelnik centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="0e0d7-356">Message center reader</span></span> | <span data-ttu-id="0e0d7-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="0e0d7-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="0e0d7-358">Administrator aplikacji pakietu Office</span><span class="sxs-lookup"><span data-stu-id="0e0d7-358">Office apps administrator</span></span> | <span data-ttu-id="0e0d7-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="0e0d7-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="0e0d7-360">Administrator haseł</span><span class="sxs-lookup"><span data-stu-id="0e0d7-360">Password administrator</span></span> | <span data-ttu-id="0e0d7-361">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="0e0d7-361">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="0e0d7-362">Power BI administrator</span><span class="sxs-lookup"><span data-stu-id="0e0d7-362">Power BI administrator</span></span> | <span data-ttu-id="0e0d7-363">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="0e0d7-363">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="0e0d7-364">platforma Power administrator</span><span class="sxs-lookup"><span data-stu-id="0e0d7-364">Power platform administrator</span></span> | <span data-ttu-id="0e0d7-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="0e0d7-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="0e0d7-366">Administrator uprzywilejowanego uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0e0d7-366">Privileged authentication administrator</span></span> | <span data-ttu-id="0e0d7-367">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="0e0d7-367">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="0e0d7-368">Administrator ról uprzywilejowanych</span><span class="sxs-lookup"><span data-stu-id="0e0d7-368">Privileged role administrator</span></span> | <span data-ttu-id="0e0d7-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="0e0d7-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="0e0d7-370">Czytelnik raportów</span><span class="sxs-lookup"><span data-stu-id="0e0d7-370">Reports reader</span></span> | <span data-ttu-id="0e0d7-371">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="0e0d7-371">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="0e0d7-372">Administrator wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0e0d7-372">Search administrator</span></span> | <span data-ttu-id="0e0d7-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="0e0d7-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="0e0d7-374">Edytor wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0e0d7-374">Search editor</span></span> | <span data-ttu-id="0e0d7-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="0e0d7-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="0e0d7-376">Administrator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0e0d7-376">Security administrator</span></span> | <span data-ttu-id="0e0d7-377">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="0e0d7-377">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="0e0d7-378">Operator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0e0d7-378">Security operator</span></span> | <span data-ttu-id="0e0d7-379">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="0e0d7-379">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="0e0d7-380">Czytelnik zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0e0d7-380">Security reader</span></span> | <span data-ttu-id="0e0d7-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="0e0d7-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="0e0d7-382">Administrator pomocy technicznej usługi</span><span class="sxs-lookup"><span data-stu-id="0e0d7-382">Service support administrator</span></span> | <span data-ttu-id="0e0d7-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="0e0d7-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="0e0d7-384">Administrator programu SharePoint</span><span class="sxs-lookup"><span data-stu-id="0e0d7-384">SharePoint administrator</span></span> | <span data-ttu-id="0e0d7-385">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="0e0d7-385">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="0e0d7-386">Administrator programu Skype dla firm</span><span class="sxs-lookup"><span data-stu-id="0e0d7-386">Skype for Business administrator</span></span> | <span data-ttu-id="0e0d7-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="0e0d7-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="0e0d7-388">Administratorzy zespołu ds. komunikacji</span><span class="sxs-lookup"><span data-stu-id="0e0d7-388">Teams Communications Administrator</span></span> | <span data-ttu-id="0e0d7-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="0e0d7-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="0e0d7-390">Inżynierowie pomocy technicznej dla zespołów</span><span class="sxs-lookup"><span data-stu-id="0e0d7-390">Teams Communications Support Engineer</span></span> | <span data-ttu-id="0e0d7-391">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="0e0d7-391">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="0e0d7-392">Zespoły komunikacyjne specjalisty</span><span class="sxs-lookup"><span data-stu-id="0e0d7-392">Teams Communications Specialist</span></span> | <span data-ttu-id="0e0d7-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="0e0d7-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="0e0d7-394">Administrator usługi Teams</span><span class="sxs-lookup"><span data-stu-id="0e0d7-394">Teams Service Administrator</span></span> | <span data-ttu-id="0e0d7-395">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="0e0d7-395">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="0e0d7-396">Administrator użytkowników</span><span class="sxs-lookup"><span data-stu-id="0e0d7-396">User administrator</span></span> | <span data-ttu-id="0e0d7-397">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="0e0d7-397">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e0d7-398">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0e0d7-398">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
