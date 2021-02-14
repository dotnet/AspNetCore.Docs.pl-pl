---
title: Użyj interfejs API programu Graph z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak używać interfejs API programu Graph z Blazor aplikacjami WebAssemlby.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 997d4dec05ddb6b9d0acb5ed36a6510c0836a4fb
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280500"
---
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="b06cc-103">Użyj interfejs API programu Graph z ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b06cc-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b06cc-104">[Microsoft Graph API](/graph/use-the-api) to interfejs API sieci Web RESTful, który umożliwia Blazor aplikacjom .NET Framework dostęp do Microsoft Cloud zasobów usług.</span><span class="sxs-lookup"><span data-stu-id="b06cc-104">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="b06cc-105">Zestaw Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b06cc-105">Graph SDK</span></span>

<span data-ttu-id="b06cc-106">[Microsoft Graph SDK](/graph/sdks/sdks-overview) zostały zaprojektowane w celu uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które mają dostęp Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b06cc-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="b06cc-107">Przykłady w tej sekcji wymagają odwołań do pakietów dla następujących pakietów w pliku projektu autonomicznej lub *`Client`* pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b06cc-107">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="b06cc-108">Następujące klasy narzędzi i konfiguracja są używane w każdej z poniższych podsekcji tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="b06cc-108">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="b06cc-109">Wywoływanie interfejs API programu Graph ze składnika przy użyciu zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b06cc-109">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="b06cc-110">Dostosowywanie oświadczeń użytkowników przy użyciu zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b06cc-110">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="b06cc-111">Po dodaniu zakresów interfejsu API Microsoft Graph w obszarze usługi AAD Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b06cc-111">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="b06cc-112">Dodaj następującą `GraphClientExtensions.cs` klasę do aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="b06cc-112">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="b06cc-113">Podaj wymagane zakresy do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> właściwości <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> w `AuthenticateRequestAsync` metodzie.</span><span class="sxs-lookup"><span data-stu-id="b06cc-113">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="b06cc-114">W poniższym przykładzie `User.Read` zakres jest określony, aby dopasować przykłady w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="b06cc-114">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="b06cc-115">Symbole zastępcze zakresu `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` w powyższym kodzie reprezentują co najmniej jeden dozwolony zakres.</span><span class="sxs-lookup"><span data-stu-id="b06cc-115">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="b06cc-116">Na przykład ustaw `Scopes` na tablicę ciągów jednego zakresu dla `User.Read` przykładów w poniższych sekcjach tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="b06cc-116">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="b06cc-117">W programie `Program.Main` ( `Program.cs` ) Dodaj usługi i konfigurację klienta grafu przy użyciu `AddGraphClient` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="b06cc-117">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="b06cc-118">Symbole zastępcze zakresu `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` w powyższym kodzie reprezentują co najmniej jeden dozwolony zakres.</span><span class="sxs-lookup"><span data-stu-id="b06cc-118">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="b06cc-119">Na przykład przekazanie `User.Read` zakresu do `AddGraphClient` przykładów w poniższych sekcjach tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="b06cc-119">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="b06cc-120">Wywoływanie interfejs API programu Graph ze składnika przy użyciu zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b06cc-120">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="b06cc-121">W tej sekcji są stosowane [klasy narzędzi ( `GraphClientExtensions.cs` )](#graph-sdk) opisane wcześniej w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="b06cc-121">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="b06cc-122">Poniższy `GraphExample` składnik używa iniekcji `GraphServiceClient` w celu uzyskania danych profilu usługi AAD użytkownika i wyświetlenia numeru telefonu komórkowego:</span><span class="sxs-lookup"><span data-stu-id="b06cc-122">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="b06cc-123">Dostosowywanie oświadczeń użytkowników przy użyciu zestawu Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b06cc-123">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="b06cc-124">W tej sekcji są stosowane [klasy narzędzi ( `GraphClientExtensions.cs` )](#graph-sdk) opisane wcześniej w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="b06cc-124">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="b06cc-125">W poniższym przykładzie aplikacja tworzy wniosek o numer telefonu komórkowego dla użytkownika ze swojego numeru telefonu komórkowego profilu użytkownika usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="b06cc-125">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="b06cc-126">W aplikacji musi być `User.Read` skonfigurowany zakres interfejs API programu Graph w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="b06cc-126">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="b06cc-127">W poniższej fabryce niestandardowego konta użytkownika struktura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> reprezentuje konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b06cc-127">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="b06cc-128">Jeśli aplikacja wymaga niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika na <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> wartość w poniższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="b06cc-128">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="b06cc-129">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="b06cc-129">`CustomAccountFactory.cs`:</span></span>

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
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
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
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
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

<span data-ttu-id="b06cc-130">W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="b06cc-130">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a><span data-ttu-id="b06cc-131">Nazwany klient z interfejs API programu Graph</span><span class="sxs-lookup"><span data-stu-id="b06cc-131">Named client with Graph API</span></span>

<span data-ttu-id="b06cc-132">W przykładach w tej sekcji użyto nazwy <xref:System.Net.Http.HttpClient> do interfejs API programu Graph, aby uzyskać numer telefonu komórkowego użytkownika w celu przetworzenia wywołania.</span><span class="sxs-lookup"><span data-stu-id="b06cc-132">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="b06cc-133">Przykłady w tej sekcji wymagają odwołania do pakietu [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) w pliku projektu autonomicznej lub *`Client`* pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b06cc-133">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="b06cc-134">Utwórz następującą klasę i konfigurację projektu do pracy z interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="b06cc-134">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="b06cc-135">W każdej z poniższych podsekcji tego artykułu są używane następujące klasy i konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="b06cc-135">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="b06cc-136">Wywołaj interfejs API programu Graph ze składnika</span><span class="sxs-lookup"><span data-stu-id="b06cc-136">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="b06cc-137">Dostosowywanie oświadczeń użytkowników przy użyciu interfejs API programu Graph i nazwanego klienta</span><span class="sxs-lookup"><span data-stu-id="b06cc-137">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="b06cc-138">Po dodaniu zakresów interfejsu API Microsoft Graph w obszarze usługi AAD Azure Portal podaj wymagane zakresy do programu obsługi skonfigurowanej przez aplikację dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="b06cc-138">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="b06cc-139">Poniższy przykład konfiguruje procedurę obsługi dla `User.Read` zakresu.</span><span class="sxs-lookup"><span data-stu-id="b06cc-139">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="b06cc-140">Dodatkowe zakresy można dodać.</span><span class="sxs-lookup"><span data-stu-id="b06cc-140">Additional scopes can be added.</span></span>

<span data-ttu-id="b06cc-141">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="b06cc-141">`GraphAuthorizationMessageHandler.cs`:</span></span>

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
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="b06cc-142">W programie `Program.Main` ( `Program.cs` ) skonfiguruj nazwę <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph:</span><span class="sxs-lookup"><span data-stu-id="b06cc-142">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="b06cc-143">Wywołaj interfejs API programu Graph ze składnika</span><span class="sxs-lookup"><span data-stu-id="b06cc-143">Call Graph API from a component</span></span>

<span data-ttu-id="b06cc-144">W tej sekcji jest używany [program obsługi komunikatów autoryzacji programu Graph ( `GraphAuthorizationMessageHandler.cs` ) i `Program.Main` Dodatki do aplikacji](#named-client-with-graph-api) opisanej wcześniej w tym artykule, która zawiera nazwę <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="b06cc-144">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="b06cc-145">W Razor składniku:</span><span class="sxs-lookup"><span data-stu-id="b06cc-145">In a Razor component:</span></span>

* <span data-ttu-id="b06cc-146">Utwórz <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph i wydaj żądanie dotyczące danych profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b06cc-146">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="b06cc-147">`UserInfo.cs`Klasa wyznacza wymagane właściwości profilu użytkownika z <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> atrybutem i nazwą JSON używaną w usłudze AAD dla tych właściwości.</span><span class="sxs-lookup"><span data-stu-id="b06cc-147">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="b06cc-148">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="b06cc-148">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                // Use userInfo.MobilePhone and callInfo.Message to make a call

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="b06cc-149">Dostosowywanie oświadczeń użytkowników przy użyciu interfejs API programu Graph i nazwanego klienta</span><span class="sxs-lookup"><span data-stu-id="b06cc-149">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="b06cc-150">W tej sekcji jest używany [program obsługi komunikatów autoryzacji programu Graph ( `GraphAuthorizationMessageHandler.cs` ) i `Program.Main` Dodatki do aplikacji](#named-client-with-graph-api) opisanej wcześniej w tym artykule, która zawiera nazwę <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph.</span><span class="sxs-lookup"><span data-stu-id="b06cc-150">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="b06cc-151">W poniższym przykładzie aplikacja tworzy na podstawie numeru telefonu komórkowego dla użytkownika wniosek o numer telefonu komórkowego.</span><span class="sxs-lookup"><span data-stu-id="b06cc-151">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="b06cc-152">W aplikacji musi być `User.Read` skonfigurowany zakres interfejs API programu Graph w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="b06cc-152">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="b06cc-153">Dodaj `UserInfo.cs` klasę do aplikacji i Wyznacz wymagane właściwości profilu użytkownika przy użyciu <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> atrybutu oraz nazwy JSON używanej przez usługi AAD dla tych właściwości:</span><span class="sxs-lookup"><span data-stu-id="b06cc-153">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="b06cc-154">W poniższej fabryce niestandardowego konta użytkownika struktura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> reprezentuje konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b06cc-154">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="b06cc-155">Jeśli aplikacja wymaga niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika na <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> wartość w poniższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="b06cc-155">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="b06cc-156">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="b06cc-156">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="b06cc-157">W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="b06cc-157">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="b06cc-158">Powyższy przykład dotyczy aplikacji korzystającej z uwierzytelniania usługi AAD z MSAL.</span><span class="sxs-lookup"><span data-stu-id="b06cc-158">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="b06cc-159">Podobne wzorce istnieją dla OIDC i uwierzytelniania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="b06cc-159">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="b06cc-160">Aby uzyskać więcej informacji, zobacz przykłady w sekcji [Dostosowywanie użytkownika przy użyciu roszczeń ładunku](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) .</span><span class="sxs-lookup"><span data-stu-id="b06cc-160">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
