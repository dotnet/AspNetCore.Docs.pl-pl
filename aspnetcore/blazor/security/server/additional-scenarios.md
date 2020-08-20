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
ms.openlocfilehash: 62cf16e3e495e3e9a015a3be4e7fffd6a3ce08bc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626470"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server dodatkowe scenariusze zabezpieczeń

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a>Przekazywanie tokenów do Blazor Server aplikacji

Tokeny dostępne poza Razor składnikami w Blazor Server aplikacji mogą być przesyłane do składników z podejściem opisanym w tej sekcji. Przykładowy kod, łącznie z kompletnym `Startup.ConfigureServices` przykładem, można znaleźć w temacie [przekazywanie tokenów do Blazor aplikacji po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).

Uwierzytelniaj Blazor Server aplikację w taki sam sposób, jak w przypadku zwykłych Razor stron lub aplikacji MVC. Zainicjuj obsługę administracyjną i Zapisz tokeny na potrzeby uwierzytelniania cookie . Na przykład:

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

W usłudze, która wykonuje bezpieczne żądanie interfejsu API, wsuń dostawcę tokenu i Pobierz token, aby wywołać interfejs API:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a>Ustawianie schematu uwierzytelniania

W przypadku aplikacji, która używa więcej niż jednego oprogramowania pośredniczącego uwierzytelniania i w ten sposób ma więcej niż jeden schemat uwierzytelniania, schemat, którego Blazor używa można jawnie ustawić w konfiguracji punktu końcowego `Startup.Configure` . Poniższy przykład ustawia schemat Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Korzystanie z punktów końcowych OpenID Connect Connect (OIDC) v 2.0

Biblioteka i szablony uwierzytelniania Blazor korzystają z punktów końcowych OpenID Connect Connect (OIDC) v 1.0. Aby użyć punktu końcowego v 2.0, skonfiguruj <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opcję w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

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

Identyfikator URI identyfikatora aplikacji można znaleźć w opisie rejestracji aplikacji dostawcy OIDC.
