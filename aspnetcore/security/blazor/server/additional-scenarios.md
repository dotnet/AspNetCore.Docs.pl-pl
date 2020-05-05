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
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="3f5c3-103">Dodatkowe Blazor scenariusze zabezpieczeń ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="3f5c3-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="3f5c3-104">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="3f5c3-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="3f5c3-105">Przekazywanie tokenów do Blazor aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="3f5c3-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="3f5c3-106">Tokeny dostępne poza Razor składnikami w aplikacji Blazor serwerowej mogą być przesyłane do składników z podejściem opisanym w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3f5c3-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="3f5c3-107">Przykładowy kod, łącznie z kompletnym `Startup.ConfigureServices` przykładem, można znaleźć w temacie [przekazywanie tokenów do Blazor aplikacji po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="3f5c3-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="3f5c3-108">Uwierzytelnianie aplikacji Blazor serwerowej tak jak w przypadku zwykłych Razor stron lub aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="3f5c3-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="3f5c3-109">Zainicjuj obsługę administracyjną i Zapisz tokeny w pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="3f5c3-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="3f5c3-110">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3f5c3-110">For example:</span></span>

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

<span data-ttu-id="3f5c3-111">Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:</span><span class="sxs-lookup"><span data-stu-id="3f5c3-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="3f5c3-112">Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana Blazor w aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="3f5c3-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="3f5c3-113">W `Startup.ConfigureServices`programie Dodaj usługi dla:</span><span class="sxs-lookup"><span data-stu-id="3f5c3-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="3f5c3-114">W pliku *_Host. cshtml* Utwórz i wystąpienie `InitialApplicationState` i przekaż go jako parametr do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="3f5c3-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="3f5c3-115">W `App` składniku (*App. Razor*) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:</span><span class="sxs-lookup"><span data-stu-id="3f5c3-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="3f5c3-116">W usłudze, która wykonuje bezpieczne żądanie interfejsu API, wsuń dostawcę tokenu i Pobierz token, aby wywołać interfejs API:</span><span class="sxs-lookup"><span data-stu-id="3f5c3-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
