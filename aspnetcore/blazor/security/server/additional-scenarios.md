---
title: ASP.NET Core Blazor Server dodatkowe scenariusze zabezpieczeń
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor Server dodatkowe scenariusze zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: c8bce9572f0c21fdbd7ed585772c2b7965f40598
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592946"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="dfedc-103">ASP.NET Core Blazor Server dodatkowe scenariusze zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="dfedc-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="dfedc-104">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="dfedc-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="dfedc-105">Przekazywanie tokenów do Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="dfedc-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="dfedc-106">Tokeny dostępne poza Razor składnikami w Blazor Server aplikacji mogą być przesyłane do składników z podejściem opisanym w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="dfedc-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="dfedc-107">Przykładowy kod, łącznie z kompletnym `Startup.ConfigureServices` przykładem, można znaleźć w temacie [przekazywanie tokenów do Blazor aplikacji po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="dfedc-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="dfedc-108">Uwierzytelniaj Blazor Server aplikację w taki sam sposób, jak w przypadku zwykłych Razor stron lub aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="dfedc-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="dfedc-109">Zainicjuj obsługę administracyjną i Zapisz tokeny na potrzeby uwierzytelniania cookie .</span><span class="sxs-lookup"><span data-stu-id="dfedc-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="dfedc-110">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="dfedc-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="dfedc-111">Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:</span><span class="sxs-lookup"><span data-stu-id="dfedc-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="dfedc-112">Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana w Blazor aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="dfedc-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="dfedc-113">W programie `Startup.ConfigureServices` Dodaj usługi dla:</span><span class="sxs-lookup"><span data-stu-id="dfedc-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="dfedc-114">W `_Host.cshtml` pliku Utwórz i wystąpienie `InitialApplicationState` i przekaż je jako parametr do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="dfedc-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="dfedc-115">W `App` składniku ( `App.razor` ) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:</span><span class="sxs-lookup"><span data-stu-id="dfedc-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="dfedc-116">Dodaj odwołanie do pakietu do aplikacji dla pakietu NuGet [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .</span><span class="sxs-lookup"><span data-stu-id="dfedc-116">Add a package reference to the app for the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="dfedc-117">W usłudze, która wykonuje bezpieczne żądanie interfejsu API, wsuń dostawcę tokenu i Pobierz token, aby wywołać interfejs API:</span><span class="sxs-lookup"><span data-stu-id="dfedc-117">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="dfedc-118">Ustawianie schematu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="dfedc-118">Set the authentication scheme</span></span>

<span data-ttu-id="dfedc-119">W przypadku aplikacji, która używa więcej niż jednego oprogramowania pośredniczącego uwierzytelniania i w ten sposób ma więcej niż jeden schemat uwierzytelniania, schemat, którego Blazor używa można jawnie ustawić w konfiguracji punktu końcowego `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="dfedc-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="dfedc-120">Poniższy przykład ustawia schemat Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="dfedc-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="dfedc-121">Korzystanie z punktów końcowych OpenID Connect Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="dfedc-121">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="dfedc-122">Biblioteka i szablony uwierzytelniania Blazor korzystają z punktów końcowych OpenID Connect Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="dfedc-122">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="dfedc-123">Aby użyć punktu końcowego v 2.0, skonfiguruj <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opcję w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="dfedc-123">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="dfedc-124">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="dfedc-124">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="dfedc-125">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="dfedc-125">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="dfedc-126">Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> lub w pliku ustawień aplikacji na <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klucz.</span><span class="sxs-lookup"><span data-stu-id="dfedc-126">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="dfedc-127">Zmiany kodu</span><span class="sxs-lookup"><span data-stu-id="dfedc-127">Code changes</span></span>

* <span data-ttu-id="dfedc-128">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="dfedc-128">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="dfedc-129">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="dfedc-129">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="dfedc-130">w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="dfedc-130">in the Azure documentation.</span></span>
* <span data-ttu-id="dfedc-131">Ponieważ zasoby są określone w identyfikatorach URI zakresu dla punktów końcowych v 2.0, Usuń <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> ustawienie właściwości w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="dfedc-131">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="dfedc-132">Identyfikator URI identyfikatora aplikacji można znaleźć w opisie rejestracji aplikacji dostawcy OIDC.</span><span class="sxs-lookup"><span data-stu-id="dfedc-132">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
