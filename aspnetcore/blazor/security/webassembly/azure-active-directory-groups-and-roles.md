---
title: ASP.NET Core Blazor WebAssembly z grupami Azure Active Directory i rolami
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor WebAssembly program, aby używać grup i ról Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ded70f028b3021574ba260838837d9b23abd72f1
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981885"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="89913-103">Grupy Azure Active Directory (AAD), role administratorów i role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="89913-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="89913-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="89913-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="89913-105">Ten artykuł ma zastosowanie do Blazor aplikacji ASP.NET Core w wersji 3,1 z programem Microsoft Identity v 1.0 i została zaplanowana do aktualizacji na 5,0 przy użyciu wersji Identity 2.0.</span><span class="sxs-lookup"><span data-stu-id="89913-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity v1.0 and is scheduled for update to 5.0 with Identity v2.0.</span></span> <span data-ttu-id="89913-106">Aby uzyskać więcej informacji, zobacz [ Blazor WASM with AAD/B2C Groups and Roles (#17683 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span><span class="sxs-lookup"><span data-stu-id="89913-106">For more information, see [Blazor WASM with AAD/B2C groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span></span>

<span data-ttu-id="89913-107">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="89913-107">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="89913-108">Grupy zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="89913-108">User-defined groups</span></span>
  * <span data-ttu-id="89913-109">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="89913-109">Security</span></span>
  * <span data-ttu-id="89913-110">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="89913-110">Microsoft 365</span></span>
  * <span data-ttu-id="89913-111">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="89913-111">Distribution</span></span>
* <span data-ttu-id="89913-112">Role</span><span class="sxs-lookup"><span data-stu-id="89913-112">Roles</span></span>
  * <span data-ttu-id="89913-113">Role administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="89913-113">AAD Administrator Roles</span></span>
  * <span data-ttu-id="89913-114">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="89913-114">User-defined roles</span></span>

<span data-ttu-id="89913-115">Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="89913-115">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="89913-116">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="89913-116">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="89913-117">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="89913-117">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="89913-118">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="89913-118">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="89913-119">Zakresy</span><span class="sxs-lookup"><span data-stu-id="89913-119">Scopes</span></span>

<span data-ttu-id="89913-120">Wywołanie [interfejsu API Microsoft Graph](/graph/use-the-api) jest wymagane dla wszystkich użytkowników aplikacji mających więcej niż pięć ról administratora usługi AAD i członkostwa w grupach zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="89913-120">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="89913-121">Aby zezwolić na interfejs API programu Graph wywołań, nadaje autonomiczną lub *`Client`* aplikację hostowanego Blazor rozwiązania dowolne z następujących [uprawnień interfejs API programu Graph (zakresy)](/graph/permissions-reference) w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="89913-121">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="89913-122">`Directory.Read.All` jest zakresem najniższych uprawnień i jest zakresem używanym dla przykładu opisanego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="89913-122">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="89913-123">Grupy zdefiniowane przez użytkownika i role administratorów</span><span class="sxs-lookup"><span data-stu-id="89913-123">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="89913-124">Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="89913-124">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="89913-125">Przypisywanie użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i ról administratorów usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="89913-125">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="89913-126">Role korzystające z grup zabezpieczeń usługi Azure AD</span><span class="sxs-lookup"><span data-stu-id="89913-126">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="89913-127">`groupMembershipClaims` przypisane</span><span class="sxs-lookup"><span data-stu-id="89913-127">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="89913-128">W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="89913-128">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="89913-129">Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="89913-129">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="89913-130">Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="89913-130">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="89913-131">Gdy liczba przypisanych ról administratora usługi AAD i grup zdefiniowanych przez użytkownika przekracza pięć, w usłudze AAD zostanie wysłane zgłoszenie do `hasgroups` `true` wartości zamiast wysyłania `groups` żądania.</span><span class="sxs-lookup"><span data-stu-id="89913-131">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="89913-132">Wszystkie aplikacje, które mogą mieć więcej niż pięć ról i grup przypisanych do swoich użytkowników, muszą wykonać oddzielne wywołanie interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="89913-132">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="89913-133">Przykładowa implementacja podana w tym artykule dotyczy tego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="89913-133">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="89913-134">Aby uzyskać więcej informacji, zobacz `groups` `hasgroups` artykuł i informacje o oświadczeniach w [tokenach dostępu do platformy tożsamości firmy Microsoft: oświadczenie dotyczące ładunku](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="89913-134">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="89913-135"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.</span><span class="sxs-lookup"><span data-stu-id="89913-135">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="89913-136">Przypisz pustą tablicę do każdej właściwości, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach później.</span><span class="sxs-lookup"><span data-stu-id="89913-136">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="89913-137">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="89913-137">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="89913-138">Użyj **jednego** z poniższych metod, aby utworzyć oświadczenia dla grup i ról usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="89913-138">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="89913-139">Korzystanie z zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="89913-139">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="89913-140">Użyj nazwanego `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="89913-140">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="89913-141">Korzystanie z zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="89913-141">Use the Graph SDK</span></span>

<span data-ttu-id="89913-142">Dodaj odwołanie do pakietu do aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania dla programu [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="89913-142">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="89913-143">Dodaj klasy i konfigurację narzędzia zestawu Graph SDK w sekcji *zestaw Graph SDK* <xref:blazor/security/webassembly/graph-api#graph-sdk> artykułu.</span><span class="sxs-lookup"><span data-stu-id="89913-143">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="89913-144">Dodaj następującą niestandardową fabrykę kont użytkowników do autonomicznej appo lub *`Client`* aplikacji hostowanego Blazor rozwiązania ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="89913-144">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="89913-145">Niestandardowa fabryka użytkowników służy do przetwarzania oświadczeń ról i grup.</span><span class="sxs-lookup"><span data-stu-id="89913-145">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="89913-146">`roles`Tablica roszczeń znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="89913-146">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="89913-147">Jeśli `hasgroups` jest obecny, zestaw Graph SDK jest używany do autoryzowania żądania interfejs API programu Graph w celu uzyskania ról i grup użytkownika:</span><span class="sxs-lookup"><span data-stu-id="89913-147">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

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

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
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
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
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

<span data-ttu-id="89913-148">Poprzedzający kod nie obejmuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="89913-148">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="89913-149">Jeśli aplikacja wymaga bezpośrednich i przechodnich oświadczeń członkostwa w grupie:</span><span class="sxs-lookup"><span data-stu-id="89913-149">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="89913-150">Zmień `IUserMemberOfCollectionWithReferencesPage` Typ na `groupsAndAzureRoles` na `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="89913-150">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="89913-151">Podczas żądania grup i ról użytkownika Zastąp `MemberOf` Właściwość opcją `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="89913-151">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="89913-152">W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="89913-152">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="89913-153">Użyj nazwanego `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="89913-153">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="89913-154">W aplikacji autonomicznej lub *`Client`* aplikacji rozwiązania hostowanego Blazor Utwórz klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> .</span><span class="sxs-lookup"><span data-stu-id="89913-154">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="89913-155">Użyj poprawnego zakresu dla wywołań interfejs API programu Graph, które uzyskują informacje o rolach i grupach.</span><span class="sxs-lookup"><span data-stu-id="89913-155">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="89913-156">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="89913-156">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="89913-157">W programie `Program.Main` ( `Program.cs` ) Dodaj <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> usługę implementacji i Dodaj nazwę <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="89913-157">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="89913-158">Poniższy przykład nazywa klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="89913-158">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="89913-159">Utwórz klasy obiektów katalogu usługi AAD, aby otrzymywać role i grupy protokołu Open Data Protocol (OData) z wywołania interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="89913-159">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="89913-160">Dane OData docierają do formatu JSON i wywołanie do <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> wypełnienia wystąpienia `DirectoryObjects` klasy.</span><span class="sxs-lookup"><span data-stu-id="89913-160">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="89913-161">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="89913-161">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="89913-162">Utwórz niestandardową fabrykę użytkowników, aby przetwarzać oświadczenia ról i grup.</span><span class="sxs-lookup"><span data-stu-id="89913-162">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="89913-163">Poniższa przykładowa implementacja obsługuje również `roles` tablicę roszczeń, która znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="89913-163">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="89913-164">Jeśli `hasgroups` jest obecny wniosek, nazwa <xref:System.Net.Http.HttpClient> jest używana do autoryzowania żądania interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="89913-164">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="89913-165">W tej implementacji jest stosowany Identity punkt końcowy platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([Dokumentacja interfejsu API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="89913-165">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="89913-166">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="89913-166">`CustomAccountFactory.cs`:</span></span>

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
                    logger.LogError("Graph API access token failure: {Message}", 
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

<span data-ttu-id="89913-167">Nie trzeba podawać kodu, aby usunąć pierwotne `groups` zastrzeżenie, jeśli jest obecny, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="89913-167">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="89913-168">Podejście w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="89913-168">The approach in this example:</span></span>
>
> * <span data-ttu-id="89913-169">Dodaje klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , aby dołączyć tokeny dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="89913-169">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="89913-170">Dodaje nazwane <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejsu API sieci Web do bezpiecznego, zewnętrznego punktu końcowego interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="89913-170">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="89913-171">Używa nazwy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań.</span><span class="sxs-lookup"><span data-stu-id="89913-171">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="89913-172">Ogólny zakres tego podejścia znajduje się w <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artykule.</span><span class="sxs-lookup"><span data-stu-id="89913-172">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="89913-173">Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) autonomicznej aplikacji lub *`Client`* aplikacji hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="89913-173">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="89913-174">Wyrażanie zgody na `Directory.Read.All` zakres jako dodatkowy zakres aplikacji:</span><span class="sxs-lookup"><span data-stu-id="89913-174">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="89913-175">Konfiguracja autoryzacji</span><span class="sxs-lookup"><span data-stu-id="89913-175">Authorization configuration</span></span>

<span data-ttu-id="89913-176">Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="89913-176">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="89913-177">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="89913-177">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="89913-178">Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="89913-178">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="89913-179">W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="89913-179">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="89913-180">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="89913-180">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="89913-181">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="89913-181">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="89913-182">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="89913-182">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="89913-183">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="89913-183">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="89913-184">Autoryzuj dostęp do interfejsu API serwera dla grup zdefiniowanych przez użytkownika i ról administratorów</span><span class="sxs-lookup"><span data-stu-id="89913-184">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="89913-185">Oprócz autoryzowania użytkowników w aplikacji webassembly po stronie klienta w celu uzyskania dostępu do stron i zasobów, interfejs API serwera może autoryzować użytkowników w celu uzyskania dostępu do bezpiecznych punktów końcowych interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89913-185">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="89913-186">Gdy aplikacja *serwera* zweryfikuje token dostępu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="89913-186">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="89913-187">Aplikacja interfejsu API serwera używa niezmiennego [identyfikatora obiektu użytkownika ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) z tokenu dostępu, aby uzyskać token dostępu dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="89913-187">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="89913-188">Wywołanie interfejs API programu Graph uzyskuje członkostwo użytkownika zdefiniowanego przez użytkownika na platformie Azure i grupy zabezpieczeń, wywołując użytkownika [`memberOf`](/graph/api/user-list-memberof) .</span><span class="sxs-lookup"><span data-stu-id="89913-188">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="89913-189">Członkostwa są używane do ustanawiania `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="89913-189">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="89913-190">[Zasady autoryzacji](xref:security/authorization/policies) mogą służyć do ograniczania dostępu użytkowników do punktów końcowych interfejsu API serwera w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89913-190">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="89913-191">Te wskazówki nie obejmują obecnie autoryzacji użytkowników na podstawie [ról zdefiniowanych przez użytkownika usługi AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="89913-191">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="89913-192">Wskazówki zawarte w tej sekcji konfigurują aplikację interfejsu API serwera jako [*aplikację demona*](/azure/active-directory/develop/scenario-daemon-overview) dla wywołania interfejsu API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="89913-192">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="89913-193">Takie podejście **nie** jest następujące:</span><span class="sxs-lookup"><span data-stu-id="89913-193">This approach does **not**:</span></span>

* <span data-ttu-id="89913-194">Wymagaj `access_as_user` zakresu.</span><span class="sxs-lookup"><span data-stu-id="89913-194">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="89913-195">Interfejs API programu Graph dostępu w imieniu użytkownika/klienta wykonującego żądanie interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="89913-195">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="89913-196">Wywołanie interfejs API programu Graph **przez aplikację interfejsu API serwera interfejs API programu Graph wymaga** tylko, aby `Directory.Read.All` w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="89913-196">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="89913-197">Takie podejście bezproblemowo uniemożliwia aplikacji klienckiej uzyskanie dostępu do danych katalogu, których interfejs API serwera nie zezwoli jawnie.</span><span class="sxs-lookup"><span data-stu-id="89913-197">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="89913-198">Aplikacja kliencka może uzyskać dostęp tylko do punktów końcowych kontrolera aplikacji interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="89913-198">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="89913-199">Konfiguracja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="89913-199">Azure configuration</span></span>

* <span data-ttu-id="89913-200">Upewnij się, że rejestracja aplikacji *serwera* ma przydaną **aplikację** (nie **delegowaną**) interfejs API programu Graph zakresem `Directory.Read.All` , który jest poziomem dostępu o najniższych uprawnieniach dla grup zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="89913-200">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="89913-201">Upewnij się, że do zakresu zastosowana jest zgoda administratora, po wprowadzeniu przypisania zakresu.</span><span class="sxs-lookup"><span data-stu-id="89913-201">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="89913-202">Przypisz nowy wpis tajny klienta do aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="89913-202">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="89913-203">Zanotuj wpis tajny konfiguracji aplikacji w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="89913-203">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="89913-204">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="89913-204">App settings</span></span>

<span data-ttu-id="89913-205">W pliku ustawień aplikacji ( `appsettings.json` lub `appsettings.Production.json` ) Utwórz `ClientSecret` wpis z użyciem klucza tajnego klienta aplikacji *serwera* z Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="89913-205">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="89913-206">Przykład:</span><span class="sxs-lookup"><span data-stu-id="89913-206">For example:</span></span>

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
> <span data-ttu-id="89913-207">Jeśli domena wydawcy dzierżawcy nie została zweryfikowana, zakres interfejsu API serwera dla dostępu użytkownika/klienta korzysta z `https://` identyfikatora URI opartego na identyfikatorze.</span><span class="sxs-lookup"><span data-stu-id="89913-207">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="89913-208">W tym scenariuszu aplikacja interfejsu API serwera wymaga `Audience` konfiguracji w `appsettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="89913-208">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="89913-209">W poniższej konfiguracji koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/{DEFAULT SCOPE}` , gdzie symbol zastępczy `{DEFAULT SCOPE}` jest zakresem domyślnym:</span><span class="sxs-lookup"><span data-stu-id="89913-209">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
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
> <span data-ttu-id="89913-210">W powyższej przykładowej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="89913-210">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="89913-211">Domena dzierżawy to `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="89913-211">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="89913-212">Aplikacja interfejsu API serwera `ClientId` to `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="89913-212">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="89913-213">Konfigurowanie `Audience` jawnie zwykle **nie** jest wymagane w przypadku aplikacji z zweryfikowaną domeną wydawcy, która ma `api://` zakres interfejsu API opartego na systemie.</span><span class="sxs-lookup"><span data-stu-id="89913-213">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="89913-214">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="89913-214">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="89913-215">Zasady autoryzacji</span><span class="sxs-lookup"><span data-stu-id="89913-215">Authorization policies</span></span>

<span data-ttu-id="89913-216">Utwórz [zasady autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń usługi AAD i ról administratora usługi AAD *Server* w aplikacji serwera `Startup.ConfigureServices` ( `Startup.cs` ) na podstawie identyfikatorów obiektów grup i [identyfikatorów obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="89913-216">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="89913-217">Na przykład zasady roli administrator rozliczeń platformy Azure mają następującą konfigurację:</span><span class="sxs-lookup"><span data-stu-id="89913-217">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="89913-218">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="89913-218">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="89913-219">Dostęp do kontrolera</span><span class="sxs-lookup"><span data-stu-id="89913-219">Controller access</span></span>

<span data-ttu-id="89913-220">Wymagaj zasad na kontrolerach aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="89913-220">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="89913-221">Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdmin` , zgodnie z konfiguracją w sekcji [zasady autoryzacji](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="89913-221">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

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

### <a name="packages"></a><span data-ttu-id="89913-222">Pakiety</span><span class="sxs-lookup"><span data-stu-id="89913-222">Packages</span></span>

<span data-ttu-id="89913-223">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="89913-223">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="89913-224">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="89913-224">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="89913-225">[Microsoft. Identity . Klient](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="89913-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="89913-226">Usługi</span><span class="sxs-lookup"><span data-stu-id="89913-226">Services</span></span>

<span data-ttu-id="89913-227">W metodzie aplikacji *serwera* `Startup.ConfigureServices` są wymagane dodatkowe przestrzenie nazw dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="89913-227">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="89913-228">Dodaj następujące przestrzenie nazw do `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="89913-228">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="89913-229">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="89913-229">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="89913-230">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="89913-230">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="89913-231">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="89913-231">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="89913-232">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="89913-232">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="89913-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="89913-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="89913-234">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="89913-234">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="89913-235">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="89913-235">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
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
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

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
                        userIdentity.AddClaim(new Claim("group", entry.Id));
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

<span data-ttu-id="89913-236">W poprzednim kodzie Obsługa następujących błędów tokenu jest opcjonalna:</span><span class="sxs-lookup"><span data-stu-id="89913-236">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="89913-237">`MsalUiRequiredException`: Aplikacja nie ma wystarczających uprawnień (zakresów).</span><span class="sxs-lookup"><span data-stu-id="89913-237">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="89913-238">Ustal, czy zakresy aplikacji interfejsu API serwera w Azure Portal zawierają uprawnienia **aplikacji** dla programu `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="89913-238">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="89913-239">Upewnij się, że administrator dzierżawy udzielił uprawnień do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89913-239">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="89913-240">`MsalServiceException` ( `AADSTS70011` ): Upewnij się, że zakres to `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="89913-240">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="89913-241">Pakiety</span><span class="sxs-lookup"><span data-stu-id="89913-241">Packages</span></span>

<span data-ttu-id="89913-242">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="89913-242">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="89913-243">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="89913-243">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="89913-244">[Firma Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="89913-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="89913-245">Konfiguracja usługi</span><span class="sxs-lookup"><span data-stu-id="89913-245">Service configuration</span></span>

<span data-ttu-id="89913-246">W metodzie aplikacji *serwera* `Startup.ConfigureServices` Dodaj logikę, aby interfejs API programu Graph wywołać i ustanowić oświadczenia użytkowników `group` dla grup zabezpieczeń i ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="89913-246">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="89913-247">Przykładowy kod w tej sekcji używa Active Directory Authentication Library (ADAL), który jest oparty na Identity platformie Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="89913-247">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="89913-248">Dodatkowe przestrzenie nazw są wymagane dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="89913-248">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="89913-249">Poniższy zestaw `using` instrukcji zawiera przestrzenie nazw wymagane dla kodu, który następuje po tej sekcji:</span><span class="sxs-lookup"><span data-stu-id="89913-249">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="89913-250">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="89913-250">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="89913-251">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="89913-251">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="89913-252">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="89913-252">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="89913-253">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="89913-253">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="89913-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="89913-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="89913-255">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="89913-255">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="89913-256">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="89913-256">In `Startup.ConfigureServices`:</span></span>

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
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="89913-257">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="89913-257">In the preceding example:</span></span>

* <span data-ttu-id="89913-258">Najpierw podjęto próbę pozyskania tokenu dyskretnego, <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ponieważ token dostępu mógł już zostać zapisany w pamięci podręcznej tokenów biblioteki ADAL.</span><span class="sxs-lookup"><span data-stu-id="89913-258">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="89913-259">Pobieranie tokenu z pamięci podręcznej jest szybsze niż żądanie nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="89913-259">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="89913-260">Jeśli token dostępu nie zostanie uzyskany z pamięci podręcznej ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> lub <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> jest zgłaszany), potwierdzenie użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) zostanie wykonane przy użyciu poświadczeń klienta ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) w celu uzyskania tokenu w imieniu użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="89913-260">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="89913-261">Następnie można dalej `Microsoft.Graph.GraphServiceClient` używać tokenu, aby wywołać interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="89913-261">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="89913-262">Token jest umieszczany w pamięci podręcznej tokenów ADAL.</span><span class="sxs-lookup"><span data-stu-id="89913-262">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="89913-263">W przypadku przyszłych interfejs API programu Graph wywołań dla tego samego użytkownika token jest uzyskiwany z pamięci podręcznej dyskretnie z <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="89913-263">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="89913-264">Kod w <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie uzyskuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="89913-264">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="89913-265">Aby zmienić kod w celu uzyskania bezpośrednich i przechodnich członkostw:</span><span class="sxs-lookup"><span data-stu-id="89913-265">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="89913-266">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="89913-266">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="89913-267">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="89913-267">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="89913-268">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="89913-268">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="89913-269">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="89913-269">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="89913-270">Kod w programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie rozróżnia grup zabezpieczeń usługi AAD i ról administratorów usługi AAD podczas tworzenia oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="89913-270">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="89913-271">Aby można było rozróżnić grupę i role w aplikacji, należy sprawdzić `entry.ODataType` podczas iterowania przez grupy i role.</span><span class="sxs-lookup"><span data-stu-id="89913-271">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="89913-272">Aby utworzyć oddzielną grupę zabezpieczeń i oświadczenia ról, należy użyć kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="89913-272">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="89913-273">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="89913-273">User-defined roles</span></span>

<span data-ttu-id="89913-274">Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="89913-274">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="89913-275">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="89913-275">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="89913-276">W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:</span><span class="sxs-lookup"><span data-stu-id="89913-276">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="89913-277">Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="89913-277">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="89913-278">Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.</span><span class="sxs-lookup"><span data-stu-id="89913-278">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="89913-279">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="89913-279">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="89913-280">Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="89913-280">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="89913-281">Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="89913-281">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="89913-282">`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="89913-282">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="89913-283">Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania, jak pokazano w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="89913-283">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="89913-284">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="89913-284">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="89913-285">W `Program.Main` autonomicznej aplikacji lub *`Client`* aplikacji rozwiązania hostowanego Blazor należy określić `role` rolę "" jako element Claim role:</span><span class="sxs-lookup"><span data-stu-id="89913-285">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="89913-286">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="89913-286">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="89913-287">Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="89913-287">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="89913-288">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="89913-288">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="89913-289">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="89913-289">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="89913-290">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="89913-290">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="89913-291">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="89913-291">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="89913-292">Identyfikatory obiektów roli administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="89913-292">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="89913-293">Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="89913-293">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="89913-294">Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89913-294">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="89913-295">Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i role administratorów usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="89913-295">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="89913-296">Rola administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="89913-296">AAD Administrator Role</span></span> | <span data-ttu-id="89913-297">Identyfikator obiektu</span><span class="sxs-lookup"><span data-stu-id="89913-297">Object ID</span></span>
--- | ---
<span data-ttu-id="89913-298">Administrator aplikacji</span><span class="sxs-lookup"><span data-stu-id="89913-298">Application administrator</span></span> | <span data-ttu-id="89913-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="89913-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="89913-300">Deweloper aplikacji</span><span class="sxs-lookup"><span data-stu-id="89913-300">Application developer</span></span> | <span data-ttu-id="89913-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="89913-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="89913-302">Administrator uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="89913-302">Authentication administrator</span></span> | <span data-ttu-id="89913-303">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="89913-303">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="89913-304">Administrator usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="89913-304">Azure DevOps administrator</span></span> | <span data-ttu-id="89913-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="89913-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="89913-306">Azure Information Protection administrator</span><span class="sxs-lookup"><span data-stu-id="89913-306">Azure Information Protection administrator</span></span> | <span data-ttu-id="89913-307">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="89913-307">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="89913-308">Administrator zestawu kluczy B2C IEF</span><span class="sxs-lookup"><span data-stu-id="89913-308">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="89913-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="89913-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="89913-310">Administrator zasad B2C IEF</span><span class="sxs-lookup"><span data-stu-id="89913-310">B2C IEF Policy administrator</span></span> | <span data-ttu-id="89913-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="89913-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="89913-312">Administrator przepływu użytkownika B2C</span><span class="sxs-lookup"><span data-stu-id="89913-312">B2C user flow administrator</span></span> | <span data-ttu-id="89913-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="89913-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="89913-314">B2C atrybutu przepływu użytkownika</span><span class="sxs-lookup"><span data-stu-id="89913-314">B2C user flow attribute administrator</span></span> | <span data-ttu-id="89913-315">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="89913-315">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="89913-316">Administrator rozliczeń</span><span class="sxs-lookup"><span data-stu-id="89913-316">Billing administrator</span></span> | <span data-ttu-id="89913-317">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="89913-317">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="89913-318">Administrator aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="89913-318">Cloud application administrator</span></span> | <span data-ttu-id="89913-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="89913-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="89913-320">Administrator urządzenia w chmurze</span><span class="sxs-lookup"><span data-stu-id="89913-320">Cloud device administrator</span></span> | <span data-ttu-id="89913-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="89913-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="89913-322">Administrator zgodności</span><span class="sxs-lookup"><span data-stu-id="89913-322">Compliance administrator</span></span> | <span data-ttu-id="89913-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="89913-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="89913-324">Administrator danych zgodności</span><span class="sxs-lookup"><span data-stu-id="89913-324">Compliance data administrator</span></span> | <span data-ttu-id="89913-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="89913-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="89913-326">Administrator dostępu warunkowego</span><span class="sxs-lookup"><span data-stu-id="89913-326">Conditional Access administrator</span></span> | <span data-ttu-id="89913-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="89913-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="89913-328">Osoba zatwierdzająca dostęp do skrytki klienta</span><span class="sxs-lookup"><span data-stu-id="89913-328">Customer LockBox access approver</span></span> | <span data-ttu-id="89913-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="89913-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="89913-330">Administrator usługi Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="89913-330">Desktop Analytics administrator</span></span> | <span data-ttu-id="89913-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="89913-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="89913-332">Czytelnicy katalogów</span><span class="sxs-lookup"><span data-stu-id="89913-332">Directory readers</span></span> | <span data-ttu-id="89913-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="89913-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="89913-334">Administrator systemu Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="89913-334">Dynamics 365 administrator</span></span> | <span data-ttu-id="89913-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="89913-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="89913-336">Administrator programu Exchange</span><span class="sxs-lookup"><span data-stu-id="89913-336">Exchange administrator</span></span> | <span data-ttu-id="89913-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="89913-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="89913-338">IdentityAdministrator dostawcy zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="89913-338">External Identity Provider administrator</span></span> | <span data-ttu-id="89913-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="89913-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="89913-340">Administrator globalny</span><span class="sxs-lookup"><span data-stu-id="89913-340">Global administrator</span></span> | <span data-ttu-id="89913-341">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="89913-341">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="89913-342">Czytnik globalny</span><span class="sxs-lookup"><span data-stu-id="89913-342">Global reader</span></span> | <span data-ttu-id="89913-343">f6903b21-6aba-4124-B44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="89913-343">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="89913-344">Administrator grup</span><span class="sxs-lookup"><span data-stu-id="89913-344">Groups administrator</span></span> | <span data-ttu-id="89913-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="89913-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="89913-346">Zapraszający gościa</span><span class="sxs-lookup"><span data-stu-id="89913-346">Guest inviter</span></span> | <span data-ttu-id="89913-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="89913-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="89913-348">Administrator pomocy technicznej</span><span class="sxs-lookup"><span data-stu-id="89913-348">Helpdesk administrator</span></span> | <span data-ttu-id="89913-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="89913-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="89913-350">Administrator usługi Intune</span><span class="sxs-lookup"><span data-stu-id="89913-350">Intune administrator</span></span> | <span data-ttu-id="89913-351">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="89913-351">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="89913-352">Usługi kaizala administrator</span><span class="sxs-lookup"><span data-stu-id="89913-352">Kaizala administrator</span></span> | <span data-ttu-id="89913-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="89913-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="89913-354">Administrator licencji</span><span class="sxs-lookup"><span data-stu-id="89913-354">License administrator</span></span> | <span data-ttu-id="89913-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="89913-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="89913-356">Czytnik prywatności centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="89913-356">Message center privacy reader</span></span> | <span data-ttu-id="89913-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="89913-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="89913-358">Czytelnik centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="89913-358">Message center reader</span></span> | <span data-ttu-id="89913-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="89913-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="89913-360">Administrator aplikacji pakietu Office</span><span class="sxs-lookup"><span data-stu-id="89913-360">Office apps administrator</span></span> | <span data-ttu-id="89913-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="89913-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="89913-362">Administrator haseł</span><span class="sxs-lookup"><span data-stu-id="89913-362">Password administrator</span></span> | <span data-ttu-id="89913-363">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="89913-363">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="89913-364">Power BI administrator</span><span class="sxs-lookup"><span data-stu-id="89913-364">Power BI administrator</span></span> | <span data-ttu-id="89913-365">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="89913-365">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="89913-366">platforma Power administrator</span><span class="sxs-lookup"><span data-stu-id="89913-366">Power platform administrator</span></span> | <span data-ttu-id="89913-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="89913-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="89913-368">Administrator uprzywilejowanego uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="89913-368">Privileged authentication administrator</span></span> | <span data-ttu-id="89913-369">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="89913-369">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="89913-370">Administrator ról uprzywilejowanych</span><span class="sxs-lookup"><span data-stu-id="89913-370">Privileged role administrator</span></span> | <span data-ttu-id="89913-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="89913-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="89913-372">Czytelnik raportów</span><span class="sxs-lookup"><span data-stu-id="89913-372">Reports reader</span></span> | <span data-ttu-id="89913-373">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="89913-373">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="89913-374">Administrator wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="89913-374">Search administrator</span></span> | <span data-ttu-id="89913-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="89913-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="89913-376">Edytor wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="89913-376">Search editor</span></span> | <span data-ttu-id="89913-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="89913-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="89913-378">Administrator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="89913-378">Security administrator</span></span> | <span data-ttu-id="89913-379">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="89913-379">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="89913-380">Operator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="89913-380">Security operator</span></span> | <span data-ttu-id="89913-381">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="89913-381">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="89913-382">Czytelnik zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="89913-382">Security reader</span></span> | <span data-ttu-id="89913-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="89913-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="89913-384">Administrator pomocy technicznej usługi</span><span class="sxs-lookup"><span data-stu-id="89913-384">Service support administrator</span></span> | <span data-ttu-id="89913-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="89913-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="89913-386">Administrator programu SharePoint</span><span class="sxs-lookup"><span data-stu-id="89913-386">SharePoint administrator</span></span> | <span data-ttu-id="89913-387">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="89913-387">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="89913-388">Administrator programu Skype dla firm</span><span class="sxs-lookup"><span data-stu-id="89913-388">Skype for Business administrator</span></span> | <span data-ttu-id="89913-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="89913-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="89913-390">Administratorzy zespołu ds. komunikacji</span><span class="sxs-lookup"><span data-stu-id="89913-390">Teams Communications Administrator</span></span> | <span data-ttu-id="89913-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="89913-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="89913-392">Inżynierowie pomocy technicznej dla zespołów</span><span class="sxs-lookup"><span data-stu-id="89913-392">Teams Communications Support Engineer</span></span> | <span data-ttu-id="89913-393">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="89913-393">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="89913-394">Zespoły komunikacyjne specjalisty</span><span class="sxs-lookup"><span data-stu-id="89913-394">Teams Communications Specialist</span></span> | <span data-ttu-id="89913-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="89913-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="89913-396">Administrator usługi Teams</span><span class="sxs-lookup"><span data-stu-id="89913-396">Teams Service Administrator</span></span> | <span data-ttu-id="89913-397">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="89913-397">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="89913-398">Administrator użytkowników</span><span class="sxs-lookup"><span data-stu-id="89913-398">User administrator</span></span> | <span data-ttu-id="89913-399">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="89913-399">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89913-400">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="89913-400">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
