---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: dodatkowe scenariusze zabezpieczeń'
author: guardrex
description: 'Dowiedz się, jak skonfigurować :::no-loc(Blazor Server)::: dodatkowe scenariusze zabezpieczeń.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234441"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="1ba77-103">ASP.NET Core :::no-loc(Blazor Server)::: dodatkowe scenariusze zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="1ba77-103">ASP.NET Core :::no-loc(Blazor Server)::: additional security scenarios</span></span>

<span data-ttu-id="1ba77-104">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1ba77-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="1ba77-105">Przekazywanie tokenów do :::no-loc(Blazor Server)::: aplikacji</span><span class="sxs-lookup"><span data-stu-id="1ba77-105">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="1ba77-106">Tokeny dostępne poza :::no-loc(Razor)::: składnikami w :::no-loc(Blazor Server)::: aplikacji mogą być przesyłane do składników z podejściem opisanym w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="1ba77-106">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="1ba77-107">Uwierzytelniaj :::no-loc(Blazor Server)::: aplikację w taki sam sposób, jak w przypadku zwykłych :::no-loc(Razor)::: stron lub aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="1ba77-107">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="1ba77-108">Zainicjuj obsługę administracyjną i Zapisz tokeny na potrzeby uwierzytelniania :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="1ba77-108">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="1ba77-109">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1ba77-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="1ba77-110">Opcjonalnie dodatkowe zakresy są dodawane z `options.Scope.Add("{SCOPE}");` , gdzie symbol zastępczy `{SCOPE}` jest dodatkowym zakresem do dodania.</span><span class="sxs-lookup"><span data-stu-id="1ba77-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="1ba77-111">Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana w :::no-loc(Blazor)::: aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="1ba77-111">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1ba77-112">W programie `Startup.ConfigureServices` Dodaj usługi dla:</span><span class="sxs-lookup"><span data-stu-id="1ba77-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="1ba77-113">Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:</span><span class="sxs-lookup"><span data-stu-id="1ba77-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1ba77-114">W `_Host.cshtml` pliku Utwórz i wystąpienie `InitialApplicationState` i przekaż je jako parametr do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1ba77-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="1ba77-115">W `App` składniku ( `App.razor` ) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:</span><span class="sxs-lookup"><span data-stu-id="1ba77-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="1ba77-116">Dodaj odwołanie do pakietu do aplikacji dla [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="1ba77-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="1ba77-117">W usłudze, która wykonuje bezpieczne żądanie interfejsu API, należy wstrzyknąć dostawcę tokenu i pobrać token dla żądania interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="1ba77-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="1ba77-118">Ustawianie schematu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1ba77-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="1ba77-119">W przypadku aplikacji, która używa więcej niż jednego oprogramowania pośredniczącego uwierzytelniania i w ten sposób ma więcej niż jeden schemat uwierzytelniania, schemat, którego :::no-loc(Blazor)::: używa można jawnie ustawić w konfiguracji punktu końcowego `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="1ba77-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="1ba77-120">Poniższy przykład ustawia schemat Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="1ba77-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="1ba77-121">Przekazywanie tokenów do :::no-loc(Blazor Server)::: aplikacji</span><span class="sxs-lookup"><span data-stu-id="1ba77-121">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="1ba77-122">Tokeny dostępne poza :::no-loc(Razor)::: składnikami w :::no-loc(Blazor Server)::: aplikacji mogą być przesyłane do składników z podejściem opisanym w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="1ba77-122">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="1ba77-123">Uwierzytelniaj :::no-loc(Blazor Server)::: aplikację w taki sam sposób, jak w przypadku zwykłych :::no-loc(Razor)::: stron lub aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="1ba77-123">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="1ba77-124">Zainicjuj obsługę administracyjną i Zapisz tokeny na potrzeby uwierzytelniania :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="1ba77-124">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="1ba77-125">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1ba77-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="1ba77-126">Opcjonalnie dodatkowe zakresy są dodawane z `options.Scope.Add("{SCOPE}");` , gdzie symbol zastępczy `{SCOPE}` jest dodatkowym zakresem do dodania.</span><span class="sxs-lookup"><span data-stu-id="1ba77-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="1ba77-127">Opcjonalnie zasób jest określany przy użyciu `options.Resource = "{RESOURCE}";` , gdzie symbol zastępczy `{RESOURCE}` jest zasobem.</span><span class="sxs-lookup"><span data-stu-id="1ba77-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="1ba77-128">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1ba77-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="1ba77-129">Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:</span><span class="sxs-lookup"><span data-stu-id="1ba77-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1ba77-130">Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana w :::no-loc(Blazor)::: aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="1ba77-130">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1ba77-131">W programie `Startup.ConfigureServices` Dodaj usługi dla:</span><span class="sxs-lookup"><span data-stu-id="1ba77-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="1ba77-132">W `_Host.cshtml` pliku Utwórz i wystąpienie `InitialApplicationState` i przekaż je jako parametr do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1ba77-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="1ba77-133">W `App` składniku ( `App.razor` ) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:</span><span class="sxs-lookup"><span data-stu-id="1ba77-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="1ba77-134">Dodaj odwołanie do pakietu do aplikacji dla [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="1ba77-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="1ba77-135">W usłudze, która wykonuje bezpieczne żądanie interfejsu API, należy wstrzyknąć dostawcę tokenu i pobrać token dla żądania interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="1ba77-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="1ba77-136">Ustawianie schematu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1ba77-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="1ba77-137">W przypadku aplikacji, która używa więcej niż jednego oprogramowania pośredniczącego uwierzytelniania i w ten sposób ma więcej niż jeden schemat uwierzytelniania, schemat, którego :::no-loc(Blazor)::: używa można jawnie ustawić w konfiguracji punktu końcowego `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="1ba77-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="1ba77-138">Poniższy przykład ustawia schemat Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="1ba77-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="1ba77-139">Korzystanie z punktów końcowych OpenID Connect Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="1ba77-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="1ba77-140">W wersjach ASP.NET Core wcześniejszych niż 5,0 Biblioteka i szablony uwierzytelniania korzystają z :::no-loc(Blazor)::: punktów końcowych OpenID Connect Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="1ba77-140">In versions of ASP.NET Core prior to 5.0, the authentication library and :::no-loc(Blazor)::: templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="1ba77-141">Aby użyć punktu końcowego v 2.0 z wersjami ASP.NET Core przed 5,0, skonfiguruj <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opcję w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="1ba77-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="1ba77-142">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji ( `:::no-loc(appsettings.json):::` ):</span><span class="sxs-lookup"><span data-stu-id="1ba77-142">Alternatively, the setting can be made in the app settings (`:::no-loc(appsettings.json):::`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="1ba77-143">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="1ba77-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="1ba77-144">Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> lub w pliku ustawień aplikacji na <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klucz.</span><span class="sxs-lookup"><span data-stu-id="1ba77-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="1ba77-145">Zmiany kodu</span><span class="sxs-lookup"><span data-stu-id="1ba77-145">Code changes</span></span>

* <span data-ttu-id="1ba77-146">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="1ba77-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="1ba77-147">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="1ba77-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="1ba77-148">w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1ba77-148">in the Azure documentation.</span></span>
* <span data-ttu-id="1ba77-149">Ponieważ zasoby są określone w identyfikatorach URI zakresu dla punktów końcowych v 2.0, Usuń <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> ustawienie właściwości w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="1ba77-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="1ba77-150">Aby uzyskać więcej informacji, zobacz [zakresy, nie zasoby](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1ba77-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="1ba77-151">Identyfikator URI identyfikatora aplikacji</span><span class="sxs-lookup"><span data-stu-id="1ba77-151">App ID URI</span></span>

* <span data-ttu-id="1ba77-152">W przypadku korzystania z punktów końcowych v 2.0 interfejsy API definiuje *`App ID URI`* , który jest przeznaczony do reprezentowania unikatowego identyfikatora dla interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1ba77-152">When using v2.0 endpoints, APIs define an *`App ID URI`* , which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="1ba77-153">Wszystkie zakresy obejmują identyfikator URI aplikacji jako prefiks, a punkty końcowe w wersji 2.0 emitują tokeny dostępu z IDENTYFIKATORem URI aplikacji jako odbiorcy.</span><span class="sxs-lookup"><span data-stu-id="1ba77-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="1ba77-154">W przypadku korzystania z punktów końcowych V 2.0 identyfikator klienta skonfigurowany w interfejsie API serwera zmienia się z identyfikatora aplikacji interfejsu API (identyfikator klienta) na identyfikator URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1ba77-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="1ba77-155">`:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="1ba77-155">`:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="1ba77-156">Identyfikator URI identyfikatora aplikacji można znaleźć w opisie rejestracji aplikacji dostawcy OIDC.</span><span class="sxs-lookup"><span data-stu-id="1ba77-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
