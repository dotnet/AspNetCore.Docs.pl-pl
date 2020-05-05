---
title: Dodatkowe Blazor scenariusze zabezpieczeń ASP.NET Core Server
author: guardrex
description: Dowiedz się, Blazor jak skonfigurować serwer pod kątem dodatkowych scenariuszy zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772807"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Dodatkowe Blazor scenariusze zabezpieczeń ASP.NET Core Server

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Przekazywanie tokenów do Blazor aplikacji serwera

Tokeny dostępne poza Razor składnikami w aplikacji Blazor serwerowej mogą być przesyłane do składników z podejściem opisanym w tej sekcji. Przykładowy kod, łącznie z kompletnym `Startup.ConfigureServices` przykładem, można znaleźć w temacie [przekazywanie tokenów do Blazor aplikacji po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).

Uwierzytelnianie aplikacji Blazor serwerowej tak jak w przypadku zwykłych Razor stron lub aplikacji MVC. Zainicjuj obsługę administracyjną i Zapisz tokeny w pliku cookie uwierzytelniania. Przykład:

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

Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana Blazor w aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

W `Startup.ConfigureServices`programie Dodaj usługi dla:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

W pliku *_Host. cshtml* Utwórz i wystąpienie `InitialApplicationState` i przekaż go jako parametr do aplikacji:

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

W `App` składniku (*App. Razor*) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:

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
