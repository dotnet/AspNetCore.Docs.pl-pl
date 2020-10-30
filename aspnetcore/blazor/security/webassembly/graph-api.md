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
ms.openlocfilehash: c346a80008b411e9fbbc584f7906227ab836d5fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055207"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>Użyj interfejs API programu Graph z ASP.NET Core Blazor WebAssembly

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

[Microsoft Graph API](/graph/use-the-api) to interfejs API sieci Web RESTful, który umożliwia Blazor aplikacjom .NET Framework dostęp do Microsoft Cloud zasobów usług.

## <a name="graph-sdk"></a>Zestaw Graph SDK

[Microsoft Graph SDK](/graph/sdks/sdks-overview) zostały zaprojektowane w celu uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które mają dostęp Microsoft Graph.

Przykłady w tej sekcji wymagają odwołań do pakietów dla następujących pakietów w pliku projektu autonomicznej lub *`Client`* pliku projektu aplikacji:

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

Następujące klasy narzędzi i konfiguracja są używane w każdej z poniższych podsekcji tego artykułu:

* [Wywoływanie interfejs API programu Graph ze składnika przy użyciu zestawu Graph SDK](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Dostosowywanie oświadczeń użytkowników przy użyciu zestawu Graph SDK](#customize-user-claims-with-the-graph-sdk)

Po dodaniu zakresów interfejsu API Microsoft Graph w obszarze usługi AAD Azure Portal:

* Dodaj następującą `GraphClientExtensions.cs` klasę do aplikacji autonomicznej lub *`Client`* aplikacji hostowanego Blazor rozwiązania.
* Podaj wymagane zakresy do <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> właściwości <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> w `AuthenticateRequestAsync` metodzie. W poniższym przykładzie `User.Read` zakres jest określony, aby dopasować przykłady w dalszej części tego artykułu.

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
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider Provider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await Provider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
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
        private readonly HttpClient client;

        public HttpClientHttpProvider(HttpClient client)
        {
            this.client = client;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return client.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return client.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

Symbol zastępczy `{STRING ARRAY OF SCOPES}` w poprzednim kodzie jest tablicą ciągów dozwolonych zakresów. Na przykład ustaw `Scopes` `User.Read` zakres dla przykładów w poniższych sekcjach tego artykułu:

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

W programie `Program.Main` ( `Program.cs` ) Dodaj usługi i konfigurację klienta grafu przy użyciu `AddGraphClient` metody rozszerzenia:

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

Symbol zastępczy `{STRING ARRAY OF SCOPES}` w poprzednim kodzie jest tablicą ciągów dozwolonych zakresów. Na przykład przekazanie `User.Read` zakresu do `AddGraphClient` przykładów w poniższych sekcjach tego artykułu:

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Wywoływanie interfejs API programu Graph ze składnika przy użyciu zestawu Graph SDK

W tej sekcji są stosowane [klasy narzędzi ( `GraphClientExtensions.cs` )](#graph-sdk) opisane wcześniej w tym artykule. Poniższy `GraphExample` składnik używa iniekcji `GraphServiceClient` w celu uzyskania danych profilu usługi AAD użytkownika i wyświetlenia numeru telefonu komórkowego:

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

### <a name="customize-user-claims-with-the-graph-sdk"></a>Dostosowywanie oświadczeń użytkowników przy użyciu zestawu Graph SDK

W tej sekcji są stosowane [klasy narzędzi ( `GraphClientExtensions.cs` )](#graph-sdk) opisane wcześniej w tym artykule.

W poniższym przykładzie aplikacja tworzy wniosek o numer telefonu komórkowego dla użytkownika ze swojego numeru telefonu komórkowego profilu użytkownika usługi AAD. W aplikacji musi być `User.Read` skonfigurowany zakres interfejs API programu Graph w usłudze AAD.

W poniższej fabryce niestandardowego konta użytkownika struktura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> reprezentuje konto użytkownika. Jeśli aplikacja wymaga niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika na <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> wartość w poniższym kodzie.

`CustomAccountFactory.cs`:

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

W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:

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

## <a name="named-client-with-graph-api"></a>Nazwany klient z interfejs API programu Graph

W przykładach w tej sekcji użyto nazwy <xref:System.Net.Http.HttpClient> do interfejs API programu Graph, aby uzyskać numer telefonu komórkowego użytkownika w celu przetworzenia wywołania.

Przykłady w tej sekcji wymagają odwołania do pakietu [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) w pliku projektu autonomicznej lub *`Client`* pliku projektu aplikacji.

Utwórz następującą klasę i konfigurację projektu do pracy z interfejs API programu Graph. W każdej z poniższych podsekcji tego artykułu są używane następujące klasy i konfiguracje:

* [Wywołaj interfejs API programu Graph ze składnika](#call-graph-api-from-a-component)
* [Dostosowywanie oświadczeń użytkowników przy użyciu interfejs API programu Graph i nazwanego klienta](#customize-user-claims-with-graph-api-and-a-named-client)

Po dodaniu zakresów interfejsu API Microsoft Graph w obszarze usługi AAD Azure Portal podaj wymagane zakresy do programu obsługi skonfigurowanej przez aplikację dla interfejs API programu Graph. Poniższy przykład konfiguruje procedurę obsługi dla `User.Read` zakresu. Dodatkowe zakresy można dodać.

`GraphAuthorizationMessageHandler.cs`:

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

W programie `Program.Main` ( `Program.cs` ) skonfiguruj nazwę <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Wywołaj interfejs API programu Graph ze składnika

W tej sekcji jest używany [program obsługi komunikatów autoryzacji programu Graph ( `GraphAuthorizationMessageHandler.cs` ) i `Program.Main` Dodatki do aplikacji](#named-client-with-graph-api) opisanej wcześniej w tym artykule, która zawiera nazwę <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph.

W Razor składniku:

* Utwórz <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph i wydaj żądanie dotyczące danych profilu użytkownika.
* `UserInfo.cs`Klasa wyznacza wymagane właściwości profilu użytkownika z <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> atrybutem i nazwą JSON używaną w usłudze AAD dla tych właściwości.

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

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
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

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

> [!NOTE]
> W poprzednim przykładzie deweloper implementuje niestandardowe `ICallProcessor` ( `CallProcessor` ) do kolejki, a następnie umieszcza automatyczne wywołania.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Dostosowywanie oświadczeń użytkowników przy użyciu interfejs API programu Graph i nazwanego klienta

W tej sekcji jest używany [program obsługi komunikatów autoryzacji programu Graph ( `GraphAuthorizationMessageHandler.cs` ) i `Program.Main` Dodatki do aplikacji](#named-client-with-graph-api) opisanej wcześniej w tym artykule, która zawiera nazwę <xref:System.Net.Http.HttpClient> dla interfejs API programu Graph.

W poniższym przykładzie aplikacja tworzy na podstawie numeru telefonu komórkowego dla użytkownika wniosek o numer telefonu komórkowego. W aplikacji musi być `User.Read` skonfigurowany zakres interfejs API programu Graph w usłudze AAD.

Dodaj `UserInfo.cs` klasę do aplikacji i Wyznacz wymagane właściwości profilu użytkownika przy użyciu <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> atrybutu oraz nazwy JSON używanej przez usługi AAD dla tych właściwości:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

W poniższej fabryce niestandardowego konta użytkownika struktura <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> reprezentuje konto użytkownika. Jeśli aplikacja wymaga niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika na <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> wartość w poniższym kodzie.

`CustomAccountFactory.cs`:

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

W programie `Program.Main` ( `Program.cs` ) Skonfiguruj uwierzytelnianie MSAL, aby używać fabryki niestandardowego konta użytkownika: Jeśli aplikacja używa niestandardowej klasy konta użytkownika, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , należy zamienić klasę niestandardowego konta użytkownika dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> w następującym kodzie:

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

Powyższy przykład dotyczy aplikacji korzystającej z uwierzytelniania usługi AAD z MSAL. Podobne wzorce istnieją dla OIDC i uwierzytelniania interfejsu API. Aby uzyskać więcej informacji, zobacz przykłady w sekcji [Dostosowywanie użytkownika przy użyciu roszczeń ładunku](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) .
