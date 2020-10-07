---
title: ASP.NET Core Blazor Server dodatkowe scenariusze zabezpieczeń
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor Server dodatkowe scenariusze zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
ms.openlocfilehash: 89288f3fce2dbb6f2647693ba8aaf29500b5bb2b
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805495"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server dodatkowe scenariusze zabezpieczeń

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Przekazywanie tokenów do Blazor Server aplikacji</h2>

Tokeny dostępne poza Razor składnikami w Blazor Server aplikacji mogą być przesyłane do składników z podejściem opisanym w tej sekcji.

Uwierzytelniaj Blazor Server aplikację w taki sam sposób, jak w przypadku zwykłych Razor stron lub aplikacji MVC. Zainicjuj obsługę administracyjną i Zapisz tokeny na potrzeby uwierzytelniania cookie . Na przykład:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Opcjonalnie dodatkowe zakresy są dodawane z `options.Scope.Add("{SCOPE}");` , gdzie symbol zastępczy `{SCOPE}` jest dodatkowym zakresem do dodania.

Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana w Blazor aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

W programie `Startup.ConfigureServices` Dodaj usługi dla:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

W `_Host.cshtml` pliku Utwórz i wystąpienie `InitialApplicationState` i przekaż je jako parametr do aplikacji:

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

W `App` składniku ( `App.razor` ) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:

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

Dodaj odwołanie do pakietu do aplikacji dla [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pakietu NuGet.

W usłudze, która wykonuje bezpieczne żądanie interfejsu API, należy wstrzyknąć dostawcę tokenu i pobrać token dla żądania interfejsu API:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Ustawianie schematu uwierzytelniania</h2>

W przypadku aplikacji, która używa więcej niż jednego oprogramowania pośredniczącego uwierzytelniania i w ten sposób ma więcej niż jeden schemat uwierzytelniania, schemat, którego Blazor używa można jawnie ustawić w konfiguracji punktu końcowego `Startup.Configure` . Poniższy przykład ustawia schemat Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Przekazywanie tokenów do Blazor Server aplikacji</h2>

Tokeny dostępne poza Razor składnikami w Blazor Server aplikacji mogą być przesyłane do składników z podejściem opisanym w tej sekcji.

Uwierzytelniaj Blazor Server aplikację w taki sam sposób, jak w przypadku zwykłych Razor stron lub aplikacji MVC. Zainicjuj obsługę administracyjną i Zapisz tokeny na potrzeby uwierzytelniania cookie . Na przykład:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Opcjonalnie dodatkowe zakresy są dodawane z `options.Scope.Add("{SCOPE}");` , gdzie symbol zastępczy `{SCOPE}` jest dodatkowym zakresem do dodania.

Opcjonalnie zasób jest określany przy użyciu `options.Resource = "{RESOURCE}";` , gdzie symbol zastępczy `{RESOURCE}` jest zasobem. Na przykład:

```csharp
options.Resource = "https://graph.microsoft.com";
```

Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana w Blazor aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

W programie `Startup.ConfigureServices` Dodaj usługi dla:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

W `_Host.cshtml` pliku Utwórz i wystąpienie `InitialApplicationState` i przekaż je jako parametr do aplikacji:

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

W `App` składniku ( `App.razor` ) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:

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

Dodaj odwołanie do pakietu do aplikacji dla [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pakietu NuGet.

W usłudze, która wykonuje bezpieczne żądanie interfejsu API, należy wstrzyknąć dostawcę tokenu i pobrać token dla żądania interfejsu API:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Ustawianie schematu uwierzytelniania</h2>

W przypadku aplikacji, która używa więcej niż jednego oprogramowania pośredniczącego uwierzytelniania i w ten sposób ma więcej niż jeden schemat uwierzytelniania, schemat, którego Blazor używa można jawnie ustawić w konfiguracji punktu końcowego `Startup.Configure` . Poniższy przykład ustawia schemat Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Korzystanie z punktów końcowych OpenID Connect Connect (OIDC) v 2.0

W wersjach ASP.NET Core wcześniejszych niż 5,0 Biblioteka i szablony uwierzytelniania korzystają z Blazor punktów końcowych OpenID Connect Connect (OIDC) v 1.0. Aby użyć punktu końcowego v 2.0 z wersjami ASP.NET Core przed 5,0, skonfiguruj <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opcję w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji ( `appsettings.json` ):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio. Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> lub w pliku ustawień aplikacji na <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klucz.

### <a name="code-changes"></a>Zmiany kodu

* Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0. Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) w dokumentacji platformy Azure.
* Ponieważ zasoby są określone w identyfikatorach URI zakresu dla punktów końcowych v 2.0, Usuń <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> ustawienie właściwości w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  Aby uzyskać więcej informacji, zobacz [zakresy, nie zasoby](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) w dokumentacji platformy Azure.

### <a name="app-id-uri"></a>Identyfikator URI identyfikatora aplikacji

* W przypadku korzystania z punktów końcowych v 2.0 interfejsy API definiuje *`App ID URI`* , który jest przeznaczony do reprezentowania unikatowego identyfikatora dla interfejsu API.
* Wszystkie zakresy obejmują identyfikator URI aplikacji jako prefiks, a punkty końcowe w wersji 2.0 emitują tokeny dostępu z IDENTYFIKATORem URI aplikacji jako odbiorcy.
* W przypadku korzystania z punktów końcowych V 2.0 identyfikator klienta skonfigurowany w interfejsie API serwera zmienia się z identyfikatora aplikacji interfejsu API (identyfikator klienta) na identyfikator URI aplikacji.

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

Identyfikator URI identyfikatora aplikacji można znaleźć w opisie rejestracji aplikacji dostawcy OIDC.

::: moniker-end
