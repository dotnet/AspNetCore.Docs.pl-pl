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
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058262"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="177ad-103">Grupy Azure Active Directory (AAD), role administratorów i role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="177ad-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="177ad-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="177ad-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="177ad-105">Ten artykuł ma zastosowanie do Blazor ASP.NET Core aplikacji w wersji 3,1 z firmą Microsoft Identity 1,0 i wkrótce zostanie zaktualizowany do 5,0 z Identity 2,0.</span><span class="sxs-lookup"><span data-stu-id="177ad-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="177ad-106">Aby uzyskać więcej informacji, zobacz następujący problem z usługą GitHub i żądanie ściągnięcia.</span><span class="sxs-lookup"><span data-stu-id="177ad-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="177ad-107">Karta **zmiany plików** w żądaniu ściągnięcia zawiera wersję roboczą tekstu i przykłady aktualizacji artykułu.</span><span class="sxs-lookup"><span data-stu-id="177ad-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="177ad-108">Po przejrzeniu i ostatecznych aktualizacjach żądanie ściągnięcia zostanie scalone z zestawem dokumentacji na żywo.</span><span class="sxs-lookup"><span data-stu-id="177ad-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="177ad-109">Problem: [ Blazor WASM z grupami i rolami usługi AAD (#17683 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="177ad-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="177ad-110">Żądanie ściągnięcia: [ Blazor temat grupy i role usługi AAD 5,0 (#20856 dotnet/AspNetCore.Docs)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="177ad-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="177ad-111">Azure Active Directory (AAD) oferuje kilka metod autoryzacji, które mogą być połączone z ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="177ad-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="177ad-112">Grupy zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="177ad-112">User-defined groups</span></span>
  * <span data-ttu-id="177ad-113">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="177ad-113">Security</span></span>
  * <span data-ttu-id="177ad-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="177ad-114">Microsoft 365</span></span>
  * <span data-ttu-id="177ad-115">Dystrybucja</span><span class="sxs-lookup"><span data-stu-id="177ad-115">Distribution</span></span>
* <span data-ttu-id="177ad-116">Role</span><span class="sxs-lookup"><span data-stu-id="177ad-116">Roles</span></span>
  * <span data-ttu-id="177ad-117">Role administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="177ad-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="177ad-118">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="177ad-118">User-defined roles</span></span>

<span data-ttu-id="177ad-119">Wskazówki zawarte w tym artykule dotyczą Blazor WebAssembly scenariuszy wdrażania usługi AAD, które opisano w następujących tematach:</span><span class="sxs-lookup"><span data-stu-id="177ad-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="177ad-120">Autonomiczne z kontami Microsoft</span><span class="sxs-lookup"><span data-stu-id="177ad-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="177ad-121">Autonomiczne z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="177ad-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="177ad-122">Hostowane z usługą AAD</span><span class="sxs-lookup"><span data-stu-id="177ad-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="177ad-123">Zakresy</span><span class="sxs-lookup"><span data-stu-id="177ad-123">Scopes</span></span>

<span data-ttu-id="177ad-124">Wywołanie [interfejsu API Microsoft Graph](/graph/use-the-api) jest wymagane dla wszystkich użytkowników aplikacji mających więcej niż pięć ról administratora usługi AAD i członkostwa w grupach zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="177ad-124">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="177ad-125">Aby zezwolić na interfejs API programu Graph wywołań, nadaje autonomiczną lub *`Client`* aplikację hostowanego Blazor rozwiązania dowolne z następujących [uprawnień interfejs API programu Graph (zakresy)](/graph/permissions-reference) w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="177ad-125">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="177ad-126">`Directory.Read.All` jest zakresem najniższych uprawnień i jest zakresem używanym dla przykładu opisanego w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="177ad-126">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="177ad-127">Grupy zdefiniowane przez użytkownika i role administratorów</span><span class="sxs-lookup"><span data-stu-id="177ad-127">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="177ad-128">Aby skonfigurować aplikację w Azure Portal w celu uzyskania `groups` żądania członkostwa, zobacz następujące artykuły platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="177ad-128">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="177ad-129">Przypisywanie użytkowników do grup usługi AAD zdefiniowanych przez użytkownika i ról administratorów usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="177ad-129">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="177ad-130">Role korzystające z grup zabezpieczeń usługi Azure AD</span><span class="sxs-lookup"><span data-stu-id="177ad-130">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="177ad-131">`groupMembershipClaims` przypisane</span><span class="sxs-lookup"><span data-stu-id="177ad-131">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="177ad-132">W poniższych przykładach założono, że użytkownik jest przypisany do roli *administratora rozliczeń* usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="177ad-132">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="177ad-133">Pojedyncze zgłoszenie `groups` wysyłane przez usługi AAD przedstawia grupy i role użytkownika jako identyfikatory obiektów (GUID) w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="177ad-133">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="177ad-134">Aplikacja musi skonwertować tablicę JSON grup i ról na poszczególne `group` oświadczenia, dla których aplikacja może tworzyć [zasady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="177ad-134">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="177ad-135">Gdy liczba przypisanych ról administratora usługi AAD i grup zdefiniowanych przez użytkownika przekracza pięć, w usłudze AAD zostanie wysłane zgłoszenie do `hasgroups` `true` wartości zamiast wysyłania `groups` żądania.</span><span class="sxs-lookup"><span data-stu-id="177ad-135">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="177ad-136">Wszystkie aplikacje, które mogą mieć więcej niż pięć ról i grup przypisanych do swoich użytkowników, muszą wykonać oddzielne wywołanie interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="177ad-136">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="177ad-137">Przykładowa implementacja podana w tym artykule dotyczy tego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="177ad-137">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="177ad-138">Aby uzyskać więcej informacji, zobacz `groups` `hasgroups` artykuł i informacje o oświadczeniach w [tokenach dostępu do platformy tożsamości firmy Microsoft: oświadczenie dotyczące ładunku](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="177ad-138">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="177ad-139"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>Umożliwia dołączenie właściwości tablicy dla grup i ról.</span><span class="sxs-lookup"><span data-stu-id="177ad-139">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="177ad-140">Przypisz pustą tablicę do każdej właściwości, aby sprawdzanie `null` nie było wymagane, gdy te właściwości są używane w `foreach` pętlach później.</span><span class="sxs-lookup"><span data-stu-id="177ad-140">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="177ad-141">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="177ad-141">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="177ad-142">Użyj **jednego** z poniższych metod, aby utworzyć oświadczenia dla grup i ról usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="177ad-142">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="177ad-143">Korzystanie z zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="177ad-143">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="177ad-144">Użyj nazwanego `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="177ad-144">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="177ad-145">Korzystanie z zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="177ad-145">Use the Graph SDK</span></span>

<span data-ttu-id="177ad-146">Dodaj odwołanie do pakietu do aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania dla programu [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="177ad-146">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="177ad-147">Dodaj klasy i konfigurację narzędzia zestawu Graph SDK w sekcji *zestaw Graph SDK* <xref:blazor/security/webassembly/graph-api#graph-sdk> artykułu.</span><span class="sxs-lookup"><span data-stu-id="177ad-147">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="177ad-148">Dodaj następującą niestandardową fabrykę kont użytkowników do autonomicznej appo lub *`Client`* aplikacji hostowanego Blazor rozwiązania ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="177ad-148">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="177ad-149">Niestandardowa fabryka użytkowników służy do przetwarzania oświadczeń ról i grup.</span><span class="sxs-lookup"><span data-stu-id="177ad-149">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="177ad-150">`roles`Tablica roszczeń znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="177ad-150">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="177ad-151">Jeśli `hasgroups` jest obecny, zestaw Graph SDK jest używany do autoryzowania żądania interfejs API programu Graph w celu uzyskania ról i grup użytkownika:</span><span class="sxs-lookup"><span data-stu-id="177ad-151">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

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

<span data-ttu-id="177ad-152">Poprzedzający kod nie obejmuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="177ad-152">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="177ad-153">Jeśli aplikacja wymaga bezpośrednich i przechodnich oświadczeń członkostwa w grupie:</span><span class="sxs-lookup"><span data-stu-id="177ad-153">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="177ad-154">Zmień `IUserMemberOfCollectionWithReferencesPage` Typ na `groupsAndAzureRoles` na `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="177ad-154">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="177ad-155">Podczas żądania grup i ról użytkownika Zastąp `MemberOf` Właściwość opcją `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="177ad-155">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="177ad-156">W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="177ad-156">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="177ad-157">Użyj nazwanego `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="177ad-157">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="177ad-158">W aplikacji autonomicznej lub *`Client`* aplikacji rozwiązania hostowanego Blazor Utwórz klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> .</span><span class="sxs-lookup"><span data-stu-id="177ad-158">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="177ad-159">Użyj poprawnego zakresu dla wywołań interfejs API programu Graph, które uzyskują informacje o rolach i grupach.</span><span class="sxs-lookup"><span data-stu-id="177ad-159">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="177ad-160">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="177ad-160">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="177ad-161">W programie `Program.Main` ( `Program.cs` ) Dodaj <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> usługę implementacji i Dodaj nazwę <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="177ad-161">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="177ad-162">Poniższy przykład nazywa klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="177ad-162">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="177ad-163">Utwórz klasy obiektów katalogu usługi AAD, aby otrzymywać role i grupy protokołu Open Data Protocol (OData) z wywołania interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="177ad-163">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="177ad-164">Dane OData docierają do formatu JSON i wywołanie do <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> wypełnienia wystąpienia `DirectoryObjects` klasy.</span><span class="sxs-lookup"><span data-stu-id="177ad-164">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="177ad-165">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="177ad-165">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="177ad-166">Utwórz niestandardową fabrykę użytkowników, aby przetwarzać oświadczenia ról i grup.</span><span class="sxs-lookup"><span data-stu-id="177ad-166">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="177ad-167">Poniższa przykładowa implementacja obsługuje również `roles` tablicę roszczeń, która znajduje się w sekcji [role zdefiniowane przez użytkownika](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="177ad-167">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="177ad-168">Jeśli `hasgroups` jest obecny wniosek, nazwa <xref:System.Net.Http.HttpClient> jest używana do autoryzowania żądania interfejs API programu Graph, aby uzyskać role i grupy użytkowników.</span><span class="sxs-lookup"><span data-stu-id="177ad-168">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="177ad-169">W tej implementacji jest stosowany Identity punkt końcowy platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([Dokumentacja interfejsu API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="177ad-169">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="177ad-170">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="177ad-170">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="177ad-171">Nie trzeba podawać kodu, aby usunąć pierwotne `groups` zastrzeżenie, jeśli jest obecny, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="177ad-171">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="177ad-172">Podejście w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="177ad-172">The approach in this example:</span></span>
>
> * <span data-ttu-id="177ad-173">Dodaje klasę niestandardową <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , aby dołączyć tokeny dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="177ad-173">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="177ad-174">Dodaje nazwane <xref:System.Net.Http.HttpClient> do tworzenia żądań interfejsu API sieci Web do bezpiecznego, zewnętrznego punktu końcowego interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="177ad-174">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="177ad-175">Używa nazwy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań.</span><span class="sxs-lookup"><span data-stu-id="177ad-175">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="177ad-176">Ogólny zakres tego podejścia znajduje się w <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> artykule.</span><span class="sxs-lookup"><span data-stu-id="177ad-176">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="177ad-177">Zarejestruj fabrykę w `Program.Main` ( `Program.cs` ) autonomicznej aplikacji lub *`Client`* aplikacji hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="177ad-177">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="177ad-178">Wyrażanie zgody na `Directory.Read.All` zakres jako dodatkowy zakres aplikacji:</span><span class="sxs-lookup"><span data-stu-id="177ad-178">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="177ad-179">Konfiguracja autoryzacji</span><span class="sxs-lookup"><span data-stu-id="177ad-179">Authorization configuration</span></span>

<span data-ttu-id="177ad-180">Utwórz [zasady](xref:security/authorization/policies) dla każdej grupy lub roli w programie `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="177ad-180">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="177ad-181">Poniższy przykład tworzy zasady dla roli *administratora rozliczeń* usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="177ad-181">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="177ad-182">Aby uzyskać pełną listę identyfikatorów obiektów roli usługi AAD, zobacz sekcję [identyfikatory obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="177ad-182">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="177ad-183">W poniższych przykładach aplikacja używa powyższych zasad do autoryzowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="177ad-183">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="177ad-184">[ `AuthorizeView` Składnik](xref:blazor/security/index#authorizeview-component) współpracuje z zasadami:</span><span class="sxs-lookup"><span data-stu-id="177ad-184">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="177ad-185">Dostęp do całego składnika może opierać się na zasadach przy użyciu [ `[Authorize]` dyrektywy Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="177ad-185">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="177ad-186">Jeśli użytkownik nie jest zalogowany, nastąpi przekierowanie do strony logowania do usługi AAD, a następnie powrót do składnika po zalogowaniu się.</span><span class="sxs-lookup"><span data-stu-id="177ad-186">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="177ad-187">Sprawdzanie zasad można również [wykonać w kodzie za pomocą logiki proceduralnej](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="177ad-187">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="177ad-188">Autoryzuj dostęp do interfejsu API serwera dla grup zdefiniowanych przez użytkownika i ról administratorów</span><span class="sxs-lookup"><span data-stu-id="177ad-188">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="177ad-189">Oprócz autoryzowania użytkowników w aplikacji webassembly po stronie klienta w celu uzyskania dostępu do stron i zasobów, interfejs API serwera może autoryzować użytkowników w celu uzyskania dostępu do bezpiecznych punktów końcowych interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="177ad-189">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="177ad-190">Gdy aplikacja *serwera* zweryfikuje token dostępu użytkownika:</span><span class="sxs-lookup"><span data-stu-id="177ad-190">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="177ad-191">Aplikacja interfejsu API serwera używa niezmiennego [identyfikatora obiektu użytkownika ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) z tokenu dostępu, aby uzyskać token dostępu dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="177ad-191">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="177ad-192">Wywołanie interfejs API programu Graph uzyskuje członkostwo użytkownika zdefiniowanego przez użytkownika na platformie Azure i grupy zabezpieczeń, wywołując użytkownika [`memberOf`](/graph/api/user-list-memberof) .</span><span class="sxs-lookup"><span data-stu-id="177ad-192">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="177ad-193">Członkostwa są używane do ustanawiania `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="177ad-193">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="177ad-194">[Zasady autoryzacji](xref:security/authorization/policies) mogą służyć do ograniczania dostępu użytkowników do punktów końcowych interfejsu API serwera w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="177ad-194">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="177ad-195">Te wskazówki nie obejmują obecnie autoryzacji użytkowników na podstawie [ról zdefiniowanych przez użytkownika usługi AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="177ad-195">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="177ad-196">Wskazówki zawarte w tej sekcji konfigurują aplikację interfejsu API serwera jako [*aplikację demona*](/azure/active-directory/develop/scenario-daemon-overview) dla wywołania interfejsu API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="177ad-196">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="177ad-197">Takie podejście **nie** jest następujące:</span><span class="sxs-lookup"><span data-stu-id="177ad-197">This approach does **not**:</span></span>

* <span data-ttu-id="177ad-198">Wymagaj `access_as_user` zakresu.</span><span class="sxs-lookup"><span data-stu-id="177ad-198">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="177ad-199">Interfejs API programu Graph dostępu w imieniu użytkownika/klienta wykonującego żądanie interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="177ad-199">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="177ad-200">Wywołanie interfejs API programu Graph **przez aplikację interfejsu API serwera interfejs API programu Graph wymaga** tylko, aby `Directory.Read.All` w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="177ad-200">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="177ad-201">Takie podejście bezproblemowo uniemożliwia aplikacji klienckiej uzyskanie dostępu do danych katalogu, których interfejs API serwera nie zezwoli jawnie.</span><span class="sxs-lookup"><span data-stu-id="177ad-201">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="177ad-202">Aplikacja kliencka może uzyskać dostęp tylko do punktów końcowych kontrolera aplikacji interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="177ad-202">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="177ad-203">Konfiguracja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="177ad-203">Azure configuration</span></span>

* <span data-ttu-id="177ad-204">Upewnij się, że rejestracja aplikacji *serwera* ma przydaną **aplikację** (nie **delegowaną**) interfejs API programu Graph zakresem `Directory.Read.All` , który jest poziomem dostępu o najniższych uprawnieniach dla grup zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="177ad-204">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="177ad-205">Upewnij się, że do zakresu zastosowana jest zgoda administratora, po wprowadzeniu przypisania zakresu.</span><span class="sxs-lookup"><span data-stu-id="177ad-205">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="177ad-206">Przypisz nowy wpis tajny klienta do aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="177ad-206">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="177ad-207">Zanotuj wpis tajny konfiguracji aplikacji w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="177ad-207">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="177ad-208">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="177ad-208">App settings</span></span>

<span data-ttu-id="177ad-209">W pliku ustawień aplikacji ( `appsettings.json` lub `appsettings.Production.json` ) Utwórz `ClientSecret` wpis z użyciem klucza tajnego klienta aplikacji *serwera* z Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="177ad-209">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="177ad-210">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="177ad-210">For example:</span></span>

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
> <span data-ttu-id="177ad-211">Jeśli domena wydawcy dzierżawcy nie została zweryfikowana, zakres interfejsu API serwera dla dostępu użytkownika/klienta korzysta z `https://` identyfikatora URI opartego na identyfikatorze.</span><span class="sxs-lookup"><span data-stu-id="177ad-211">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="177ad-212">W tym scenariuszu aplikacja interfejsu API serwera wymaga `Audience` konfiguracji w `appsettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="177ad-212">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="177ad-213">W poniższej konfiguracji koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/{DEFAULT SCOPE}` , gdzie symbol zastępczy `{DEFAULT SCOPE}` jest zakresem domyślnym:</span><span class="sxs-lookup"><span data-stu-id="177ad-213">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
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
> <span data-ttu-id="177ad-214">W powyższej przykładowej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="177ad-214">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="177ad-215">Domena dzierżawy to `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="177ad-215">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="177ad-216">Aplikacja interfejsu API serwera `ClientId` to `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="177ad-216">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="177ad-217">Konfigurowanie `Audience` jawnie zwykle **nie** jest wymagane w przypadku aplikacji z zweryfikowaną domeną wydawcy, która ma `api://` zakres interfejsu API opartego na systemie.</span><span class="sxs-lookup"><span data-stu-id="177ad-217">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="177ad-218">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="177ad-218">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="177ad-219">Zasady autoryzacji</span><span class="sxs-lookup"><span data-stu-id="177ad-219">Authorization policies</span></span>

<span data-ttu-id="177ad-220">Utwórz [zasady autoryzacji](xref:security/authorization/policies) dla grup zabezpieczeń usługi AAD i ról administratora usługi AAD  w aplikacji serwera `Startup.ConfigureServices` ( `Startup.cs` ) na podstawie identyfikatorów obiektów grup i [identyfikatorów obiektów roli administratora usługi AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="177ad-220">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="177ad-221">Na przykład zasady roli administrator rozliczeń platformy Azure mają następującą konfigurację:</span><span class="sxs-lookup"><span data-stu-id="177ad-221">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="177ad-222">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="177ad-222">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="177ad-223">Dostęp do kontrolera</span><span class="sxs-lookup"><span data-stu-id="177ad-223">Controller access</span></span>

<span data-ttu-id="177ad-224">Wymagaj zasad na kontrolerach aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="177ad-224">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="177ad-225">Poniższy przykład ogranicza dostęp do danych rozliczeniowych od `BillingDataController` administratorów rozliczeń do platformy Azure z nazwą zasad `BillingAdmin` , zgodnie z konfiguracją w sekcji [zasady autoryzacji](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="177ad-225">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

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

### <a name="packages"></a><span data-ttu-id="177ad-226">Pakiety</span><span class="sxs-lookup"><span data-stu-id="177ad-226">Packages</span></span>

<span data-ttu-id="177ad-227">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="177ad-227">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="177ad-228">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="177ad-228">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="177ad-229">[Microsoft. Identity . Klient](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="177ad-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="177ad-230">Usługi</span><span class="sxs-lookup"><span data-stu-id="177ad-230">Services</span></span>

<span data-ttu-id="177ad-231">W metodzie aplikacji *serwera* `Startup.ConfigureServices` są wymagane dodatkowe przestrzenie nazw dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="177ad-231">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="177ad-232">Dodaj następujące przestrzenie nazw do `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="177ad-232">Add the following namespaces to `Startup.cs`:</span></span>

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

<span data-ttu-id="177ad-233">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="177ad-233">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="177ad-234">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="177ad-234">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="177ad-235">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="177ad-235">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="177ad-236">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="177ad-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="177ad-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="177ad-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="177ad-238">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="177ad-238">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="177ad-239">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="177ad-239">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="177ad-240">W poprzednim kodzie Obsługa następujących błędów tokenu jest opcjonalna:</span><span class="sxs-lookup"><span data-stu-id="177ad-240">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="177ad-241">`MsalUiRequiredException`: Aplikacja nie ma wystarczających uprawnień (zakresów).</span><span class="sxs-lookup"><span data-stu-id="177ad-241">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="177ad-242">Ustal, czy zakresy aplikacji interfejsu API serwera w Azure Portal zawierają uprawnienia **aplikacji** dla programu `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="177ad-242">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="177ad-243">Upewnij się, że administrator dzierżawy udzielił uprawnień do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="177ad-243">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="177ad-244">`MsalServiceException` ( `AADSTS70011` ): Upewnij się, że zakres to `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="177ad-244">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="177ad-245">Pakiety</span><span class="sxs-lookup"><span data-stu-id="177ad-245">Packages</span></span>

<span data-ttu-id="177ad-246">Dodaj odwołania do pakietu do aplikacji *serwerowej* dla następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="177ad-246">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="177ad-247">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="177ad-247">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="177ad-248">[Firma Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="177ad-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="177ad-249">Konfiguracja usługi</span><span class="sxs-lookup"><span data-stu-id="177ad-249">Service configuration</span></span>

<span data-ttu-id="177ad-250">W metodzie aplikacji *serwera* `Startup.ConfigureServices` Dodaj logikę, aby interfejs API programu Graph wywołać i ustanowić oświadczenia użytkowników `group` dla grup zabezpieczeń i ról użytkownika.</span><span class="sxs-lookup"><span data-stu-id="177ad-250">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="177ad-251">Przykładowy kod w tej sekcji używa Active Directory Authentication Library (ADAL), który jest oparty na Identity platformie Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="177ad-251">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="177ad-252">Dodatkowe przestrzenie nazw są wymagane dla kodu w `Startup` klasie aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="177ad-252">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="177ad-253">Poniższy zestaw `using` instrukcji zawiera przestrzenie nazw wymagane dla kodu, który następuje po tej sekcji:</span><span class="sxs-lookup"><span data-stu-id="177ad-253">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="177ad-254">Podczas konfigurowania <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="177ad-254">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="177ad-255">Opcjonalnie Dołącz przetwarzanie dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="177ad-255">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="177ad-256">Na przykład aplikacja może rejestrować nieudane uwierzytelnienie.</span><span class="sxs-lookup"><span data-stu-id="177ad-256">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="177ad-257">W programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> Wykonaj wywołanie interfejs API programu Graph, aby uzyskać grupy i role użytkownika.</span><span class="sxs-lookup"><span data-stu-id="177ad-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="177ad-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> udostępnia dane osobowe użytkownika w rejestrowaniu.</span><span class="sxs-lookup"><span data-stu-id="177ad-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="177ad-259">Aktywuj wyłącznie dane OSOBowe na potrzeby debugowania przy użyciu kont użytkowników testowych.</span><span class="sxs-lookup"><span data-stu-id="177ad-259">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="177ad-260">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="177ad-260">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="177ad-261">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="177ad-261">In the preceding example:</span></span>

* <span data-ttu-id="177ad-262">Najpierw podjęto próbę pozyskania tokenu dyskretnego, <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ponieważ token dostępu mógł już zostać zapisany w pamięci podręcznej tokenów biblioteki ADAL.</span><span class="sxs-lookup"><span data-stu-id="177ad-262">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="177ad-263">Pobieranie tokenu z pamięci podręcznej jest szybsze niż żądanie nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="177ad-263">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="177ad-264">Jeśli token dostępu nie zostanie uzyskany z pamięci podręcznej ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> lub <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> jest zgłaszany), potwierdzenie użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) zostanie wykonane przy użyciu poświadczeń klienta ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) w celu uzyskania tokenu w imieniu użytkownika ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="177ad-264">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="177ad-265">Następnie można dalej `Microsoft.Graph.GraphServiceClient` używać tokenu, aby wywołać interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="177ad-265">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="177ad-266">Token jest umieszczany w pamięci podręcznej tokenów ADAL.</span><span class="sxs-lookup"><span data-stu-id="177ad-266">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="177ad-267">W przypadku przyszłych interfejs API programu Graph wywołań dla tego samego użytkownika token jest uzyskiwany z pamięci podręcznej dyskretnie z <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="177ad-267">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="177ad-268">Kod w <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie uzyskuje członkostw przechodnich.</span><span class="sxs-lookup"><span data-stu-id="177ad-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="177ad-269">Aby zmienić kod w celu uzyskania bezpośrednich i przechodnich członkostw:</span><span class="sxs-lookup"><span data-stu-id="177ad-269">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="177ad-270">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="177ad-270">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="177ad-271">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="177ad-271">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="177ad-272">Dla wiersza kodu:</span><span class="sxs-lookup"><span data-stu-id="177ad-272">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="177ad-273">Zamień poprzedni wiersz na:</span><span class="sxs-lookup"><span data-stu-id="177ad-273">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="177ad-274">Kod w programie <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nie rozróżnia grup zabezpieczeń usługi AAD i ról administratorów usługi AAD podczas tworzenia oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="177ad-274">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="177ad-275">Aby można było rozróżnić grupę i role w aplikacji, należy sprawdzić `entry.ODataType` podczas iterowania przez grupy i role.</span><span class="sxs-lookup"><span data-stu-id="177ad-275">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="177ad-276">Aby utworzyć oddzielną grupę zabezpieczeń i oświadczenia ról, należy użyć kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="177ad-276">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="177ad-277">Role zdefiniowane przez użytkownika</span><span class="sxs-lookup"><span data-stu-id="177ad-277">User-defined roles</span></span>

<span data-ttu-id="177ad-278">Aplikacje zarejestrowane w usłudze AAD można również skonfigurować tak, aby korzystały z ról zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="177ad-278">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="177ad-279">Aby skonfigurować aplikację w Azure Portal w celu zapewnienia `roles` członkostwa, zobacz [How to: Dodawanie ról aplikacji w aplikacji i odbieranie ich w tokenie](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="177ad-279">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="177ad-280">W poniższym przykładzie przyjęto założenie, że aplikacja ma skonfigurowaną dwie role:</span><span class="sxs-lookup"><span data-stu-id="177ad-280">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="177ad-281">Chociaż nie możesz przypisywać ról do grup zabezpieczeń bez konta Azure AD — wersja Premium, możesz przypisywać użytkowników do ról i otrzymywać `roles` żądania dla użytkowników przy użyciu standardowego konta platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="177ad-281">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="177ad-282">Wskazówki zawarte w tej sekcji nie wymagają konta Azure AD — wersja Premium.</span><span class="sxs-lookup"><span data-stu-id="177ad-282">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="177ad-283">W Azure Portal są przypisane wiele ról przez **_ponowne dodanie użytkownika_** do każdego dodatkowego przypisania roli.</span><span class="sxs-lookup"><span data-stu-id="177ad-283">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="177ad-284">Pojedyncze zgłoszenie `roles` wysyłane przez usługi AAD przedstawia role zdefiniowane przez użytkownika jako `appRoles` `value` elementy w tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="177ad-284">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="177ad-285">Aplikacja musi skonwertować tablicę JSON ról na poszczególne `role` oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="177ad-285">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="177ad-286">`CustomUserFactory`Przedstawione w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) zostały skonfigurowane do działania w ramach `roles` roszczeń z wartością tablicy JSON.</span><span class="sxs-lookup"><span data-stu-id="177ad-286">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="177ad-287">Dodaj i zarejestruj `CustomUserFactory` w aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania, jak pokazano w sekcji [zdefiniowane przez użytkownika i role administratora usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="177ad-287">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="177ad-288">Nie ma potrzeby podania kodu w celu usunięcia pierwotnego `roles` żądania, ponieważ jest ono automatycznie usuwane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="177ad-288">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="177ad-289">W `Program.Main` autonomicznej aplikacji lub *`Client`* aplikacji rozwiązania hostowanego Blazor należy określić `role` rolę "" jako element Claim role:</span><span class="sxs-lookup"><span data-stu-id="177ad-289">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="177ad-290">Metody autoryzacji składników są w tym momencie funkcjonalne.</span><span class="sxs-lookup"><span data-stu-id="177ad-290">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="177ad-291">Każdy mechanizm autoryzacji w składnikach programu może korzystać z `admin` roli w celu autoryzowania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="177ad-291">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="177ad-292">[ `AuthorizeView` składnik](xref:blazor/security/index#authorizeview-component) (przykład: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="177ad-292">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="177ad-293">[ `[Authorize]` dyrektywa Attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (przykład: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="177ad-293">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="177ad-294">[Logika proceduralna](xref:blazor/security/index#procedural-logic) (przykład: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="177ad-294">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="177ad-295">Obsługiwane są wiele testów ról:</span><span class="sxs-lookup"><span data-stu-id="177ad-295">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="177ad-296">Identyfikatory obiektów roli administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="177ad-296">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="177ad-297">Identyfikatory obiektów przedstawione w poniższej tabeli służą do tworzenia [zasad](xref:security/authorization/policies) dla `group` oświadczeń.</span><span class="sxs-lookup"><span data-stu-id="177ad-297">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="177ad-298">Zasady umożliwiają aplikacji Autoryzowanie użytkowników w przypadku różnych działań w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="177ad-298">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="177ad-299">Aby uzyskać więcej informacji, zobacz sekcję [zdefiniowane przez użytkownika i role administratorów usługi AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="177ad-299">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="177ad-300">Rola administratora usługi AAD</span><span class="sxs-lookup"><span data-stu-id="177ad-300">AAD Administrator Role</span></span> | <span data-ttu-id="177ad-301">Identyfikator obiektu</span><span class="sxs-lookup"><span data-stu-id="177ad-301">Object ID</span></span>
--- | ---
<span data-ttu-id="177ad-302">Administrator aplikacji</span><span class="sxs-lookup"><span data-stu-id="177ad-302">Application administrator</span></span> | <span data-ttu-id="177ad-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="177ad-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="177ad-304">Deweloper aplikacji</span><span class="sxs-lookup"><span data-stu-id="177ad-304">Application developer</span></span> | <span data-ttu-id="177ad-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="177ad-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="177ad-306">Administrator uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="177ad-306">Authentication administrator</span></span> | <span data-ttu-id="177ad-307">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="177ad-307">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="177ad-308">Administrator usługi Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="177ad-308">Azure DevOps administrator</span></span> | <span data-ttu-id="177ad-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="177ad-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="177ad-310">Azure Information Protection administrator</span><span class="sxs-lookup"><span data-stu-id="177ad-310">Azure Information Protection administrator</span></span> | <span data-ttu-id="177ad-311">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="177ad-311">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="177ad-312">Administrator zestawu kluczy B2C IEF</span><span class="sxs-lookup"><span data-stu-id="177ad-312">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="177ad-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="177ad-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="177ad-314">Administrator zasad B2C IEF</span><span class="sxs-lookup"><span data-stu-id="177ad-314">B2C IEF Policy administrator</span></span> | <span data-ttu-id="177ad-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="177ad-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="177ad-316">Administrator przepływu użytkownika B2C</span><span class="sxs-lookup"><span data-stu-id="177ad-316">B2C user flow administrator</span></span> | <span data-ttu-id="177ad-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="177ad-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="177ad-318">B2C atrybutu przepływu użytkownika</span><span class="sxs-lookup"><span data-stu-id="177ad-318">B2C user flow attribute administrator</span></span> | <span data-ttu-id="177ad-319">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="177ad-319">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="177ad-320">Administrator rozliczeń</span><span class="sxs-lookup"><span data-stu-id="177ad-320">Billing administrator</span></span> | <span data-ttu-id="177ad-321">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="177ad-321">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="177ad-322">Administrator aplikacji w chmurze</span><span class="sxs-lookup"><span data-stu-id="177ad-322">Cloud application administrator</span></span> | <span data-ttu-id="177ad-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="177ad-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="177ad-324">Administrator urządzenia w chmurze</span><span class="sxs-lookup"><span data-stu-id="177ad-324">Cloud device administrator</span></span> | <span data-ttu-id="177ad-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="177ad-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="177ad-326">Administrator zgodności</span><span class="sxs-lookup"><span data-stu-id="177ad-326">Compliance administrator</span></span> | <span data-ttu-id="177ad-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="177ad-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="177ad-328">Administrator danych zgodności</span><span class="sxs-lookup"><span data-stu-id="177ad-328">Compliance data administrator</span></span> | <span data-ttu-id="177ad-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="177ad-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="177ad-330">Administrator dostępu warunkowego</span><span class="sxs-lookup"><span data-stu-id="177ad-330">Conditional Access administrator</span></span> | <span data-ttu-id="177ad-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="177ad-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="177ad-332">Osoba zatwierdzająca dostęp do skrytki klienta</span><span class="sxs-lookup"><span data-stu-id="177ad-332">Customer LockBox access approver</span></span> | <span data-ttu-id="177ad-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="177ad-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="177ad-334">Administrator usługi Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="177ad-334">Desktop Analytics administrator</span></span> | <span data-ttu-id="177ad-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="177ad-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="177ad-336">Czytelnicy katalogów</span><span class="sxs-lookup"><span data-stu-id="177ad-336">Directory readers</span></span> | <span data-ttu-id="177ad-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="177ad-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="177ad-338">Administrator systemu Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="177ad-338">Dynamics 365 administrator</span></span> | <span data-ttu-id="177ad-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="177ad-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="177ad-340">Administrator programu Exchange</span><span class="sxs-lookup"><span data-stu-id="177ad-340">Exchange administrator</span></span> | <span data-ttu-id="177ad-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="177ad-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="177ad-342">IdentityAdministrator dostawcy zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="177ad-342">External Identity Provider administrator</span></span> | <span data-ttu-id="177ad-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="177ad-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="177ad-344">Administrator globalny</span><span class="sxs-lookup"><span data-stu-id="177ad-344">Global administrator</span></span> | <span data-ttu-id="177ad-345">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="177ad-345">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="177ad-346">Czytnik globalny</span><span class="sxs-lookup"><span data-stu-id="177ad-346">Global reader</span></span> | <span data-ttu-id="177ad-347">f6903b21-6aba-4124-B44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="177ad-347">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="177ad-348">Administrator grup</span><span class="sxs-lookup"><span data-stu-id="177ad-348">Groups administrator</span></span> | <span data-ttu-id="177ad-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="177ad-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="177ad-350">Zapraszający gościa</span><span class="sxs-lookup"><span data-stu-id="177ad-350">Guest inviter</span></span> | <span data-ttu-id="177ad-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="177ad-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="177ad-352">Administrator pomocy technicznej</span><span class="sxs-lookup"><span data-stu-id="177ad-352">Helpdesk administrator</span></span> | <span data-ttu-id="177ad-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="177ad-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="177ad-354">Administrator usługi Intune</span><span class="sxs-lookup"><span data-stu-id="177ad-354">Intune administrator</span></span> | <span data-ttu-id="177ad-355">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="177ad-355">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="177ad-356">Usługi kaizala administrator</span><span class="sxs-lookup"><span data-stu-id="177ad-356">Kaizala administrator</span></span> | <span data-ttu-id="177ad-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="177ad-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="177ad-358">Administrator licencji</span><span class="sxs-lookup"><span data-stu-id="177ad-358">License administrator</span></span> | <span data-ttu-id="177ad-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="177ad-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="177ad-360">Czytnik prywatności centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="177ad-360">Message center privacy reader</span></span> | <span data-ttu-id="177ad-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="177ad-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="177ad-362">Czytelnik centrum wiadomości</span><span class="sxs-lookup"><span data-stu-id="177ad-362">Message center reader</span></span> | <span data-ttu-id="177ad-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="177ad-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="177ad-364">Administrator aplikacji pakietu Office</span><span class="sxs-lookup"><span data-stu-id="177ad-364">Office apps administrator</span></span> | <span data-ttu-id="177ad-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="177ad-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="177ad-366">Administrator haseł</span><span class="sxs-lookup"><span data-stu-id="177ad-366">Password administrator</span></span> | <span data-ttu-id="177ad-367">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="177ad-367">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="177ad-368">Power BI administrator</span><span class="sxs-lookup"><span data-stu-id="177ad-368">Power BI administrator</span></span> | <span data-ttu-id="177ad-369">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="177ad-369">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="177ad-370">platforma Power administrator</span><span class="sxs-lookup"><span data-stu-id="177ad-370">Power platform administrator</span></span> | <span data-ttu-id="177ad-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="177ad-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="177ad-372">Administrator uprzywilejowanego uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="177ad-372">Privileged authentication administrator</span></span> | <span data-ttu-id="177ad-373">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="177ad-373">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="177ad-374">Administrator ról uprzywilejowanych</span><span class="sxs-lookup"><span data-stu-id="177ad-374">Privileged role administrator</span></span> | <span data-ttu-id="177ad-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="177ad-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="177ad-376">Czytelnik raportów</span><span class="sxs-lookup"><span data-stu-id="177ad-376">Reports reader</span></span> | <span data-ttu-id="177ad-377">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="177ad-377">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="177ad-378">Administrator wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="177ad-378">Search administrator</span></span> | <span data-ttu-id="177ad-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="177ad-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="177ad-380">Edytor wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="177ad-380">Search editor</span></span> | <span data-ttu-id="177ad-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="177ad-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="177ad-382">Administrator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="177ad-382">Security administrator</span></span> | <span data-ttu-id="177ad-383">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="177ad-383">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="177ad-384">Operator zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="177ad-384">Security operator</span></span> | <span data-ttu-id="177ad-385">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="177ad-385">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="177ad-386">Czytelnik zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="177ad-386">Security reader</span></span> | <span data-ttu-id="177ad-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="177ad-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="177ad-388">Administrator pomocy technicznej usługi</span><span class="sxs-lookup"><span data-stu-id="177ad-388">Service support administrator</span></span> | <span data-ttu-id="177ad-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="177ad-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="177ad-390">Administrator programu SharePoint</span><span class="sxs-lookup"><span data-stu-id="177ad-390">SharePoint administrator</span></span> | <span data-ttu-id="177ad-391">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="177ad-391">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="177ad-392">Administrator programu Skype dla firm</span><span class="sxs-lookup"><span data-stu-id="177ad-392">Skype for Business administrator</span></span> | <span data-ttu-id="177ad-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="177ad-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="177ad-394">Administratorzy zespołu ds. komunikacji</span><span class="sxs-lookup"><span data-stu-id="177ad-394">Teams Communications Administrator</span></span> | <span data-ttu-id="177ad-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="177ad-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="177ad-396">Inżynierowie pomocy technicznej dla zespołów</span><span class="sxs-lookup"><span data-stu-id="177ad-396">Teams Communications Support Engineer</span></span> | <span data-ttu-id="177ad-397">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="177ad-397">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="177ad-398">Zespoły komunikacyjne specjalisty</span><span class="sxs-lookup"><span data-stu-id="177ad-398">Teams Communications Specialist</span></span> | <span data-ttu-id="177ad-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="177ad-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="177ad-400">Administrator usługi Teams</span><span class="sxs-lookup"><span data-stu-id="177ad-400">Teams Service Administrator</span></span> | <span data-ttu-id="177ad-401">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="177ad-401">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="177ad-402">Administrator użytkowników</span><span class="sxs-lookup"><span data-stu-id="177ad-402">User administrator</span></span> | <span data-ttu-id="177ad-403">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="177ad-403">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="177ad-404">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="177ad-404">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
