---
<span data-ttu-id="0320c-101">title: "ASP.NET Core Blazor Server — dodatkowe scenariusze zabezpieczeń" autor: Opis: "informacje o konfigurowaniu Blazor serwera pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="0320c-101">title: 'ASP.NET Core Blazor Server additional security scenarios' author: description: 'Learn how to configure Blazor Server for additional security scenarios.'</span></span>
<span data-ttu-id="0320c-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0320c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0320c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0320c-103">'Blazor'</span></span>
- <span data-ttu-id="0320c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0320c-104">'Identity'</span></span>
- <span data-ttu-id="0320c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0320c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="0320c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0320c-106">'Razor'</span></span>
- <span data-ttu-id="0320c-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="0320c-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="0320c-108">BlazorDodatkowe scenariusze zabezpieczeń ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="0320c-108">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="0320c-109">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0320c-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="0320c-110">Przekazywanie tokenów do Blazor aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="0320c-110">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="0320c-111">Tokeny dostępne poza Razor składnikami w Blazor aplikacji serwerowej mogą być przesyłane do składników z podejściem opisanym w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="0320c-111">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="0320c-112">Przykładowy kod, łącznie z kompletnym `Startup.ConfigureServices` przykładem, można znaleźć w temacie [przekazywanie tokenów do Blazor aplikacji po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="0320c-112">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="0320c-113">Uwierzytelnianie Blazor aplikacji serwerowej tak jak w przypadku zwykłych Razor stron lub aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="0320c-113">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="0320c-114">Zainicjuj obsługę administracyjną i Zapisz tokeny w pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0320c-114">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="0320c-115">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0320c-115">For example:</span></span>

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

<span data-ttu-id="0320c-116">Zdefiniuj klasę do przekazania w początkowym stanie aplikacji przy użyciu tokenów dostępu i odświeżania:</span><span class="sxs-lookup"><span data-stu-id="0320c-116">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="0320c-117">Zdefiniuj usługę dostawcy tokenów objętych **zakresem** , która może być używana w Blazor aplikacji do rozpoznawania tokenów z [iniekcji zależności (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="0320c-117">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="0320c-118">W programie `Startup.ConfigureServices` Dodaj usługi dla:</span><span class="sxs-lookup"><span data-stu-id="0320c-118">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="0320c-119">W pliku *_Host. cshtml* Utwórz i wystąpienie `InitialApplicationState` i przekaż go jako parametr do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0320c-119">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="0320c-120">W `App` składniku (*App. Razor*) Rozwiąż usługę i zainicjuj ją przy użyciu danych z parametru:</span><span class="sxs-lookup"><span data-stu-id="0320c-120">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="0320c-121">W usłudze, która wykonuje bezpieczne żądanie interfejsu API, wsuń dostawcę tokenu i Pobierz token, aby wywołać interfejs API:</span><span class="sxs-lookup"><span data-stu-id="0320c-121">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="0320c-122">Korzystanie z punktów końcowych programu Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="0320c-122">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="0320c-123">Biblioteka i szablony uwierzytelniania Blazor korzystają z punktów końcowych Open ID Connect (OIDC) w wersji 1.0.</span><span class="sxs-lookup"><span data-stu-id="0320c-123">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="0320c-124">Aby użyć punktu końcowego v 2.0, skonfiguruj <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opcję w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="0320c-124">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="0320c-125">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="0320c-125">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="0320c-126">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="0320c-126">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="0320c-127">Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> lub w pliku ustawień aplikacji na <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klucz.</span><span class="sxs-lookup"><span data-stu-id="0320c-127">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="0320c-128">Zmiany kodu</span><span class="sxs-lookup"><span data-stu-id="0320c-128">Code changes</span></span>

* <span data-ttu-id="0320c-129">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="0320c-129">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="0320c-130">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="0320c-130">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="0320c-131">w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="0320c-131">in the Azure documentation.</span></span>
* <span data-ttu-id="0320c-132">Ponieważ zasoby są określone w identyfikatorach URI zakresu dla punktów końcowych v 2.0, Usuń <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> ustawienie właściwości w <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="0320c-132">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

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

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="0320c-133">Identyfikator URI identyfikatora aplikacji można znaleźć w opisie rejestracji aplikacji dostawcy OIDC.</span><span class="sxs-lookup"><span data-stu-id="0320c-133">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
