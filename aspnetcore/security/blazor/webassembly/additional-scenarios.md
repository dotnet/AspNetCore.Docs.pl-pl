---
<span data-ttu-id="02ca4-101">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-103">'Blazor'</span></span>
- <span data-ttu-id="02ca4-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-104">'Identity'</span></span>
- <span data-ttu-id="02ca4-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-106">'Razor'</span></span>
- <span data-ttu-id="02ca4-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="02ca4-108">BlazorDodatkowe scenariusze zabezpieczeń ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="02ca4-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="02ca4-109">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="02ca4-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="02ca4-110">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="02ca4-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="02ca4-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Usługa może być używana z programem <xref:System.Net.Http.HttpClient> w celu dołączania tokenów dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="02ca4-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="02ca4-112">Tokeny są uzyskiwane przy użyciu istniejącej <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> usługi.</span><span class="sxs-lookup"><span data-stu-id="02ca4-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="02ca4-113">Jeśli nie można uzyskać tokenu, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="02ca4-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="02ca4-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>ma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="02ca4-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="02ca4-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="02ca4-116">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> konfiguruje <xref:System.Net.Http.HttpClient> w `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="02ca4-117">Dla wygody <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> jest dołączony wstępnie skonfigurowany przy użyciu adresu podstawowego aplikacji jako autoryzowany adres URL.</span><span class="sxs-lookup"><span data-stu-id="02ca4-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="02ca4-118">Szablony zestawów webassembly z włączonym uwierzytelnianiem są Blazor teraz używane <xref:System.Net.Http.IHttpClientFactory> w projekcie interfejsu API serwera w celu skonfigurowania programu <xref:System.Net.Http.HttpClient> przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="02ca4-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="02ca4-119">W przypadku, gdy klient został utworzony za pomocą programu <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> w powyższym przykładzie, <xref:System.Net.Http.HttpClient> jest dostarczane wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="02ca4-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="02ca4-120">Skonfigurowany <xref:System.Net.Http.HttpClient> jest następnie używany do autoryzowania żądań przy użyciu prostego wzorca [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="02ca4-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="02ca4-121">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="02ca4-122">Wpisane HttpClient</span><span class="sxs-lookup"><span data-stu-id="02ca4-122">Typed HttpClient</span></span>

<span data-ttu-id="02ca4-123">Można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy.</span><span class="sxs-lookup"><span data-stu-id="02ca4-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="02ca4-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="02ca4-124">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="02ca4-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="02ca4-126">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="02ca4-127">Konfigurowanie procedury obsługi HttpClient</span><span class="sxs-lookup"><span data-stu-id="02ca4-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="02ca4-128">Program obsługi można skonfigurować w taki sposób, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Aby wychodzące żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="02ca4-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="02ca4-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="02ca4-130">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="02ca4-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="02ca4-131">Jeśli Blazor aplikacja internetowa zestawu zwykle używa bezpiecznego ustawienia domyślnego <xref:System.Net.Http.HttpClient> , aplikacja może również wykonać nieuwierzytelnione lub nieautoryzowane żądania internetowego interfejsu API przez skonfigurowanie nazwanego elementu <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="02ca4-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="02ca4-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="02ca4-133">Poprzednia rejestracja jest uzupełnieniem istniejącej bezpiecznej rejestracji domyślnej <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="02ca4-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="02ca4-134">Składnik tworzy z programu, <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> Aby wykonać nieuwierzytelnione lub nieautoryzowane żądania:</span><span class="sxs-lookup"><span data-stu-id="02ca4-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="02ca4-135">Kontroler w interfejsie API serwera, `WeatherForecastNoAuthenticationController` dla poprzedniego przykładu, nie jest oznaczony [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="02ca4-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="02ca4-136">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="02ca4-136">Request additional access tokens</span></span>

<span data-ttu-id="02ca4-137">Tokeny dostępu można uzyskać ręcznie, wywołując metodę `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="02ca4-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="02ca4-138">W poniższym przykładzie dodatkowe zakresy interfejsu API usługi Azure Active Directory (AAD) Microsoft Graph są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="02ca4-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="02ca4-139">Po dodaniu Microsoft Graph uprawnień interfejsu API w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="02ca4-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="02ca4-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-140">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="02ca4-141">`IAccessTokenProvider.RequestToken`Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów.</span><span class="sxs-lookup"><span data-stu-id="02ca4-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="02ca4-142">W Razor składniku:</span><span class="sxs-lookup"><span data-stu-id="02ca4-142">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="02ca4-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>typu</span><span class="sxs-lookup"><span data-stu-id="02ca4-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="02ca4-144">`true`z `token` do użycia.</span><span class="sxs-lookup"><span data-stu-id="02ca4-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="02ca4-145">`false`Jeśli nie pobrano tokenu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="02ca4-146">HttpClient i HttpRequestMessage za pomocą opcji żądania interfejsu API pobierania</span><span class="sxs-lookup"><span data-stu-id="02ca4-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="02ca4-147">W przypadku uruchamiania w zestawie webassembly w Blazor aplikacji Webassembly [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań.</span><span class="sxs-lookup"><span data-stu-id="02ca4-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="02ca4-148">Na przykład można określić metodę HTTP i nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="02ca4-149">Poniższy składnik wysyła `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="02ca4-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="02ca4-150">Implementacja programu .NET webassembly <xref:System.Net.Http.HttpClient> używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="02ca4-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="02ca4-151">Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="02ca4-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="02ca4-152">Opcje żądania pobrania HTTP można skonfigurować przy użyciu <xref:System.Net.Http.HttpRequestMessage> metod rozszerzających pokazanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="02ca4-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="02ca4-153">Metoda rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="02ca4-153">Extension method</span></span> | <span data-ttu-id="02ca4-154">Właściwość żądania pobrania</span><span class="sxs-lookup"><span data-stu-id="02ca4-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="02ca4-155">uwierzytelniające</span><span class="sxs-lookup"><span data-stu-id="02ca4-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="02ca4-156">Chow</span><span class="sxs-lookup"><span data-stu-id="02ca4-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="02ca4-157">wyst</span><span class="sxs-lookup"><span data-stu-id="02ca4-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="02ca4-158">spójn</span><span class="sxs-lookup"><span data-stu-id="02ca4-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="02ca4-159">Można ustawić dodatkowe opcje przy użyciu bardziej generycznej <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="02ca4-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="02ca4-160">Odpowiedź HTTP jest zwykle buforowana w Blazor aplikacji Webassembly, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="02ca4-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="02ca4-161">Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy użyć <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metody rozszerzenia w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="02ca4-162">Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="02ca4-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="02ca4-163">Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="02ca4-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="02ca4-164">Podczas wysyłania poświadczeń (plików cookie/nagłówki autoryzacji) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="02ca4-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="02ca4-165">Następujące zasady obejmują konfigurację programu:</span><span class="sxs-lookup"><span data-stu-id="02ca4-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="02ca4-166">Pochodzenie żądania ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="02ca4-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="02ca4-167">Dowolna Metoda (czasownik).</span><span class="sxs-lookup"><span data-stu-id="02ca4-167">Any method (verb).</span></span>
* <span data-ttu-id="02ca4-168">`Content-Type`i `Authorization` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="02ca4-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="02ca4-169">Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header` ), Wyświetl nagłówek podczas wywoływania <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="02ca4-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="02ca4-170">Poświadczenia ustawione przez kod JavaScript po stronie klienta ( `credentials` Właściwość ustawiona na `include` ).</span><span class="sxs-lookup"><span data-stu-id="02ca4-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="02ca4-171">Aby uzyskać więcej informacji, zobacz <xref:security/cors> i składnik Tester żądania HTTP aplikacji przykładowej (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="02ca4-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="02ca4-172">Obsługa błędów żądania tokenu</span><span class="sxs-lookup"><span data-stu-id="02ca4-172">Handle token request errors</span></span>

<span data-ttu-id="02ca4-173">Gdy aplikacja jednostronicowa uwierzytelnia użytkownika przy użyciu funkcji Open ID Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w Identity postaci pliku cookie sesji, który jest ustawiany w wyniku użytkownika dostarczającego poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="02ca4-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="02ca4-174">Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny.</span><span class="sxs-lookup"><span data-stu-id="02ca4-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="02ca4-175">W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów.</span><span class="sxs-lookup"><span data-stu-id="02ca4-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="02ca4-176">W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.</span><span class="sxs-lookup"><span data-stu-id="02ca4-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="02ca4-177">Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP.</span><span class="sxs-lookup"><span data-stu-id="02ca4-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="02ca4-178">Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="02ca4-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="02ca4-179">Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="02ca4-180">Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach.</span><span class="sxs-lookup"><span data-stu-id="02ca4-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="02ca4-181">Są one częścią charakteru aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="02ca4-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="02ca4-182">SPA używający plików cookie również nie może wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="02ca4-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="02ca4-183">Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:</span><span class="sxs-lookup"><span data-stu-id="02ca4-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="02ca4-184">Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="02ca4-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="02ca4-185">Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="02ca4-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="02ca4-186">Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:</span><span class="sxs-lookup"><span data-stu-id="02ca4-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="02ca4-187">Żądanie powiodło się, a aplikacja ma prawidłowy token.</span><span class="sxs-lookup"><span data-stu-id="02ca4-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="02ca4-188">Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="02ca4-189">Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="02ca4-190">Istnieją różne podejścia z rosnącymi poziomami złożoności:</span><span class="sxs-lookup"><span data-stu-id="02ca4-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="02ca4-191">Przechowuj bieżący stan strony w magazynie sesji.</span><span class="sxs-lookup"><span data-stu-id="02ca4-191">Store the current page state in session storage.</span></span> <span data-ttu-id="02ca4-192">Przed kontynuowaniem sprawdź, czy w ramach [zdarzenia cyklu życia OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) można przywrócić stan.</span><span class="sxs-lookup"><span data-stu-id="02ca4-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="02ca4-193">Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="02ca4-194">Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.</span><span class="sxs-lookup"><span data-stu-id="02ca4-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="02ca4-195">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="02ca4-195">The following example shows how to:</span></span>

* <span data-ttu-id="02ca4-196">Zachowaj stan przed przekierowaniem do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="02ca4-197">Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="02ca4-198">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="02ca4-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="02ca4-199">Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP.</span><span class="sxs-lookup"><span data-stu-id="02ca4-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="02ca4-200">Taka sytuacja może wystąpić w przypadku korzystania z takiego elementu jak kontenera stanu i przywrócenia stanu po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="02ca4-201">Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="02ca4-202">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="02ca4-203">Dostosowywanie tras aplikacji</span><span class="sxs-lookup"><span data-stu-id="02ca4-203">Customize app routes</span></span>

<span data-ttu-id="02ca4-204">Domyślnie Biblioteka [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="02ca4-205">Trasa</span><span class="sxs-lookup"><span data-stu-id="02ca4-205">Route</span></span>                            | <span data-ttu-id="02ca4-206">Przeznaczenie</span><span class="sxs-lookup"><span data-stu-id="02ca4-206">Purpose</span></span> |
| ---
<span data-ttu-id="02ca4-207">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-208">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-209">'Blazor'</span></span>
- <span data-ttu-id="02ca4-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-210">'Identity'</span></span>
- <span data-ttu-id="02ca4-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-212">'Razor'</span></span>
- <span data-ttu-id="02ca4-213">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-214">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-215">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-216">'Blazor'</span></span>
- <span data-ttu-id="02ca4-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-217">'Identity'</span></span>
- <span data-ttu-id="02ca4-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-219">'Razor'</span></span>
- <span data-ttu-id="02ca4-220">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-221">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-222">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-223">'Blazor'</span></span>
- <span data-ttu-id="02ca4-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-224">'Identity'</span></span>
- <span data-ttu-id="02ca4-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-226">'Razor'</span></span>
- <span data-ttu-id="02ca4-227">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-228">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-229">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-230">'Blazor'</span></span>
- <span data-ttu-id="02ca4-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-231">'Identity'</span></span>
- <span data-ttu-id="02ca4-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-233">'Razor'</span></span>
- <span data-ttu-id="02ca4-234">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-235">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-236">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-237">'Blazor'</span></span>
- <span data-ttu-id="02ca4-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-238">'Identity'</span></span>
- <span data-ttu-id="02ca4-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-240">'Razor'</span></span>
- <span data-ttu-id="02ca4-241">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-242">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-243">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-244">'Blazor'</span></span>
- <span data-ttu-id="02ca4-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-245">'Identity'</span></span>
- <span data-ttu-id="02ca4-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-247">'Razor'</span></span>
- <span data-ttu-id="02ca4-248">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-249">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-250">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-251">'Blazor'</span></span>
- <span data-ttu-id="02ca4-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-252">'Identity'</span></span>
- <span data-ttu-id="02ca4-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-254">'Razor'</span></span>
- <span data-ttu-id="02ca4-255">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-256">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-257">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-258">'Blazor'</span></span>
- <span data-ttu-id="02ca4-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-259">'Identity'</span></span>
- <span data-ttu-id="02ca4-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-261">'Razor'</span></span>
- <span data-ttu-id="02ca4-262">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-263">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-264">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-265">'Blazor'</span></span>
- <span data-ttu-id="02ca4-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-266">'Identity'</span></span>
- <span data-ttu-id="02ca4-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-268">'Razor'</span></span>
- <span data-ttu-id="02ca4-269">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-270">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-271">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-272">'Blazor'</span></span>
- <span data-ttu-id="02ca4-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-273">'Identity'</span></span>
- <span data-ttu-id="02ca4-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-275">'Razor'</span></span>
- <span data-ttu-id="02ca4-276">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-277">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-278">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-279">'Blazor'</span></span>
- <span data-ttu-id="02ca4-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-280">'Identity'</span></span>
- <span data-ttu-id="02ca4-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-282">'Razor'</span></span>
- <span data-ttu-id="02ca4-283">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-284">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-285">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-286">'Blazor'</span></span>
- <span data-ttu-id="02ca4-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-287">'Identity'</span></span>
- <span data-ttu-id="02ca4-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-289">'Razor'</span></span>
- <span data-ttu-id="02ca4-290">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-291">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-292">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-293">'Blazor'</span></span>
- <span data-ttu-id="02ca4-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-294">'Identity'</span></span>
- <span data-ttu-id="02ca4-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-296">'Razor'</span></span>
- <span data-ttu-id="02ca4-297">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-298">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-299">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-300">'Blazor'</span></span>
- <span data-ttu-id="02ca4-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-301">'Identity'</span></span>
- <span data-ttu-id="02ca4-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-303">'Razor'</span></span>
- <span data-ttu-id="02ca4-304">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-304">'SignalR' uid:</span></span> 

<span data-ttu-id="02ca4-305">---------------- | ---title: "ASP.NET Core elementu Blazor webassembly dodatkowe scenariusze zabezpieczeń" Author: Description: "informacje o konfigurowaniu Blazor zestawu webassembly w celu uzyskania dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-306">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-307">'Blazor'</span></span>
- <span data-ttu-id="02ca4-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-308">'Identity'</span></span>
- <span data-ttu-id="02ca4-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-310">'Razor'</span></span>
- <span data-ttu-id="02ca4-311">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-311">'SignalR' uid:</span></span> 

<span data-ttu-id="02ca4-312">---- | | `authentication/login`           | Wyzwala operację logowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="02ca4-313">| | `authentication/login-callback`  | Obsługuje wynik operacji logowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="02ca4-314">| | `authentication/login-failed`    | Wyświetla komunikaty o błędach, gdy operacja logowania zakończy się niepowodzeniem z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="02ca4-315">| | `authentication/logout`          | Wyzwala operację wylogowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="02ca4-316">| | `authentication/logout-callback` | Obsługuje wynik operacji wylogowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="02ca4-317">| | `authentication/logout-failed`   | Wyświetla komunikaty o błędach, gdy operacja wylogowania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="02ca4-318">| | `authentication/logged-out`      | Wskazuje, że użytkownik pomyślnie wylogować się.</span><span class="sxs-lookup"><span data-stu-id="02ca4-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="02ca4-319">| | `authentication/profile`         | Wyzwala operację edytowania profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="02ca4-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="02ca4-320">| | `authentication/register`        | Wyzwala operację w celu zarejestrowania nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="02ca4-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="02ca4-321">Trasy przedstawione w powyższej tabeli można konfigurować za pośrednictwem programu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="02ca4-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="02ca4-322">Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="02ca4-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="02ca4-323">W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security` .</span><span class="sxs-lookup"><span data-stu-id="02ca4-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="02ca4-324">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="02ca4-325">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-325">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="02ca4-326">Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> parametr z jawnym akcją:</span><span class="sxs-lookup"><span data-stu-id="02ca4-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="02ca4-327">Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="02ca4-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="02ca4-328">Dostosowywanie interfejsu użytkownika uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="02ca4-328">Customize the authentication user interface</span></span>

<span data-ttu-id="02ca4-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="02ca4-330">Każdy stan można dostosować, przekazując element niestandardowe <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="02ca4-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="02ca4-331">Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="02ca4-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="02ca4-332">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="02ca4-333"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="02ca4-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="02ca4-334">Trasa</span><span class="sxs-lookup"><span data-stu-id="02ca4-334">Route</span></span>                            | <span data-ttu-id="02ca4-335">Fragment</span><span class="sxs-lookup"><span data-stu-id="02ca4-335">Fragment</span></span>                |
| ---
<span data-ttu-id="02ca4-336">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-337">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-338">'Blazor'</span></span>
- <span data-ttu-id="02ca4-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-339">'Identity'</span></span>
- <span data-ttu-id="02ca4-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-341">'Razor'</span></span>
- <span data-ttu-id="02ca4-342">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-343">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-344">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-345">'Blazor'</span></span>
- <span data-ttu-id="02ca4-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-346">'Identity'</span></span>
- <span data-ttu-id="02ca4-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-348">'Razor'</span></span>
- <span data-ttu-id="02ca4-349">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-350">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-351">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-352">'Blazor'</span></span>
- <span data-ttu-id="02ca4-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-353">'Identity'</span></span>
- <span data-ttu-id="02ca4-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-355">'Razor'</span></span>
- <span data-ttu-id="02ca4-356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-357">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-358">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-359">'Blazor'</span></span>
- <span data-ttu-id="02ca4-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-360">'Identity'</span></span>
- <span data-ttu-id="02ca4-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-362">'Razor'</span></span>
- <span data-ttu-id="02ca4-363">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-364">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-365">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-366">'Blazor'</span></span>
- <span data-ttu-id="02ca4-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-367">'Identity'</span></span>
- <span data-ttu-id="02ca4-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-369">'Razor'</span></span>
- <span data-ttu-id="02ca4-370">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-371">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-372">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-373">'Blazor'</span></span>
- <span data-ttu-id="02ca4-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-374">'Identity'</span></span>
- <span data-ttu-id="02ca4-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-376">'Razor'</span></span>
- <span data-ttu-id="02ca4-377">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-378">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-379">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-380">'Blazor'</span></span>
- <span data-ttu-id="02ca4-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-381">'Identity'</span></span>
- <span data-ttu-id="02ca4-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-383">'Razor'</span></span>
- <span data-ttu-id="02ca4-384">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-385">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-386">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-387">'Blazor'</span></span>
- <span data-ttu-id="02ca4-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-388">'Identity'</span></span>
- <span data-ttu-id="02ca4-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-390">'Razor'</span></span>
- <span data-ttu-id="02ca4-391">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-392">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-393">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-394">'Blazor'</span></span>
- <span data-ttu-id="02ca4-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-395">'Identity'</span></span>
- <span data-ttu-id="02ca4-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-397">'Razor'</span></span>
- <span data-ttu-id="02ca4-398">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-399">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-400">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-401">'Blazor'</span></span>
- <span data-ttu-id="02ca4-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-402">'Identity'</span></span>
- <span data-ttu-id="02ca4-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-404">'Razor'</span></span>
- <span data-ttu-id="02ca4-405">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-406">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-407">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-408">'Blazor'</span></span>
- <span data-ttu-id="02ca4-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-409">'Identity'</span></span>
- <span data-ttu-id="02ca4-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-411">'Razor'</span></span>
- <span data-ttu-id="02ca4-412">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-413">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-414">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-415">'Blazor'</span></span>
- <span data-ttu-id="02ca4-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-416">'Identity'</span></span>
- <span data-ttu-id="02ca4-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-418">'Razor'</span></span>
- <span data-ttu-id="02ca4-419">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-420">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-421">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-422">'Blazor'</span></span>
- <span data-ttu-id="02ca4-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-423">'Identity'</span></span>
- <span data-ttu-id="02ca4-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-425">'Razor'</span></span>
- <span data-ttu-id="02ca4-426">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-427">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-428">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-429">'Blazor'</span></span>
- <span data-ttu-id="02ca4-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-430">'Identity'</span></span>
- <span data-ttu-id="02ca4-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-432">'Razor'</span></span>
- <span data-ttu-id="02ca4-433">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-433">'SignalR' uid:</span></span> 

<span data-ttu-id="02ca4-434">---------------- | ---title: "ASP.NET Core elementu Blazor webassembly dodatkowe scenariusze zabezpieczeń" Author: Description: "informacje o konfigurowaniu Blazor zestawu webassembly w celu uzyskania dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-435">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-436">'Blazor'</span></span>
- <span data-ttu-id="02ca4-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-437">'Identity'</span></span>
- <span data-ttu-id="02ca4-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-439">'Razor'</span></span>
- <span data-ttu-id="02ca4-440">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-441">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-442">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-443">'Blazor'</span></span>
- <span data-ttu-id="02ca4-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-444">'Identity'</span></span>
- <span data-ttu-id="02ca4-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-446">'Razor'</span></span>
- <span data-ttu-id="02ca4-447">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-448">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-449">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-450">'Blazor'</span></span>
- <span data-ttu-id="02ca4-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-451">'Identity'</span></span>
- <span data-ttu-id="02ca4-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-453">'Razor'</span></span>
- <span data-ttu-id="02ca4-454">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-455">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-456">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-457">'Blazor'</span></span>
- <span data-ttu-id="02ca4-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-458">'Identity'</span></span>
- <span data-ttu-id="02ca4-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-460">'Razor'</span></span>
- <span data-ttu-id="02ca4-461">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-462">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-463">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-464">'Blazor'</span></span>
- <span data-ttu-id="02ca4-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-465">'Identity'</span></span>
- <span data-ttu-id="02ca4-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-467">'Razor'</span></span>
- <span data-ttu-id="02ca4-468">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-469">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-470">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-471">'Blazor'</span></span>
- <span data-ttu-id="02ca4-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-472">'Identity'</span></span>
- <span data-ttu-id="02ca4-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-474">'Razor'</span></span>
- <span data-ttu-id="02ca4-475">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-476">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-477">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-478">'Blazor'</span></span>
- <span data-ttu-id="02ca4-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-479">'Identity'</span></span>
- <span data-ttu-id="02ca4-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-481">'Razor'</span></span>
- <span data-ttu-id="02ca4-482">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-483">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-484">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-485">'Blazor'</span></span>
- <span data-ttu-id="02ca4-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-486">'Identity'</span></span>
- <span data-ttu-id="02ca4-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-488">'Razor'</span></span>
- <span data-ttu-id="02ca4-489">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="02ca4-490">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="02ca4-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="02ca4-491">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="02ca4-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="02ca4-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-492">'Blazor'</span></span>
- <span data-ttu-id="02ca4-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="02ca4-493">'Identity'</span></span>
- <span data-ttu-id="02ca4-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="02ca4-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="02ca4-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="02ca4-495">'Razor'</span></span>
- <span data-ttu-id="02ca4-496">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="02ca4-496">'SignalR' uid:</span></span> 

<span data-ttu-id="02ca4-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="02ca4-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="02ca4-498">Dostosowywanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="02ca4-498">Customize the user</span></span>

<span data-ttu-id="02ca4-499">Użytkownicy powiązana z aplikacją mogą być dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="02ca4-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="02ca4-500">W poniższym przykładzie Wszyscy uwierzytelnieni użytkownicy otrzymują `amr` każde z metod uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="02ca4-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="02ca4-501">Utwórz klasę rozszerzającą <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> klasę:</span><span class="sxs-lookup"><span data-stu-id="02ca4-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="02ca4-502">Utwórz fabrykę, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="02ca4-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="02ca4-503">Zarejestruj `CustomAccountFactory` dostawcę uwierzytelniania w użyciu.</span><span class="sxs-lookup"><span data-stu-id="02ca4-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="02ca4-504">Wszystkie następujące rejestracje są prawidłowe:</span><span class="sxs-lookup"><span data-stu-id="02ca4-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="02ca4-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="02ca4-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="02ca4-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="02ca4-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="02ca4-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="02ca4-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="02ca4-508">Obsługa prerenderowania przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="02ca4-508">Support prerendering with authentication</span></span>

<span data-ttu-id="02ca4-509">Po zastosowaniu wskazówek w jednej z aplikacji hostowanego Blazor zestawu webassembly Skorzystaj z poniższych instrukcji, aby utworzyć aplikację, która:</span><span class="sxs-lookup"><span data-stu-id="02ca4-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="02ca4-510">Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="02ca4-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="02ca4-511">Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="02ca4-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="02ca4-512">W klasie aplikacji klienckiej `Program` (*program.cs*) należy wykonać typowe rejestracje usługi w oddzielnym metodzie (na przykład `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="02ca4-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="02ca4-513">W aplikacji serwera `Startup.ConfigureServices` zarejestruj następujące dodatkowe usługi:</span><span class="sxs-lookup"><span data-stu-id="02ca4-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="02ca4-514">W metodzie aplikacji serwera `Startup.Configure` Zastąp [punkty końcowe. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) z [punktami końcowymi. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="02ca4-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="02ca4-515">W aplikacji serwer Utwórz folder *strony* , jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="02ca4-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="02ca4-516">Utwórz stronę *_Host. cshtml* w folderze *strony* aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="02ca4-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="02ca4-517">Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="02ca4-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="02ca4-518">Aktualizuj zawartość pliku:</span><span class="sxs-lookup"><span data-stu-id="02ca4-518">Update the file's contents:</span></span>

* <span data-ttu-id="02ca4-519">Dodaj `@page "_Host"` na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="02ca4-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="02ca4-520">Zastąp `<app>Loading...</app>` tag następującym:</span><span class="sxs-lookup"><span data-stu-id="02ca4-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="02ca4-521">Opcje aplikacji hostowanych i dostawców logowania innych firm</span><span class="sxs-lookup"><span data-stu-id="02ca4-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="02ca4-522">Podczas uwierzytelniania i autoryzowania hostowanej Blazor aplikacji sieci webassembly przy użyciu dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="02ca4-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="02ca4-523">Wybór jednego z nich zależy od danego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="02ca4-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="02ca4-524">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="02ca4-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="02ca4-525">Uwierzytelnianie użytkowników tylko w celu wywołania chronionych interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="02ca4-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="02ca4-526">Uwierzytelnij użytkownika za pomocą przepływu OAuth po stronie klienta dla dostawcy interfejsu API innej firmy:</span><span class="sxs-lookup"><span data-stu-id="02ca4-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="02ca4-527">W tym scenariuszu:</span><span class="sxs-lookup"><span data-stu-id="02ca4-527">In this scenario:</span></span>

* <span data-ttu-id="02ca4-528">Serwer hostujący aplikację nie odgrywa roli.</span><span class="sxs-lookup"><span data-stu-id="02ca4-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="02ca4-529">Nie można chronić interfejsów API na serwerze.</span><span class="sxs-lookup"><span data-stu-id="02ca4-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="02ca4-530">Aplikacja może wywoływać tylko chronione interfejsy API innych firm.</span><span class="sxs-lookup"><span data-stu-id="02ca4-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="02ca4-531">Uwierzytelnianie użytkowników za pomocą dostawcy innych firm i wywoływanie chronionych interfejsów API na serwerze hosta i stronie trzeciej</span><span class="sxs-lookup"><span data-stu-id="02ca4-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="02ca4-532">Skonfiguruj Identity przy użyciu innego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="02ca4-533">Uzyskaj tokeny wymagane przez dostęp do interfejsu API innych firm i Zapisz je.</span><span class="sxs-lookup"><span data-stu-id="02ca4-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="02ca4-534">Gdy użytkownik loguje się, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="02ca4-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="02ca4-535">W tym momencie istnieje kilka podejścia dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.</span><span class="sxs-lookup"><span data-stu-id="02ca4-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="02ca4-536">Korzystanie z tokenu dostępu do serwera w celu pobrania tokenu dostępu innej firmy</span><span class="sxs-lookup"><span data-stu-id="02ca4-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="02ca4-537">Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="02ca4-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="02ca4-538">Z tego miejsca Użyj tokenu dostępu innej firmy do wywołania zasobów interfejsu API innych firm bezpośrednio z poziomu Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="02ca4-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="02ca4-539">Nie zalecamy tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="02ca4-539">We don't recommend this approach.</span></span> <span data-ttu-id="02ca4-540">Takie podejście wymaga traktowania tokenu dostępu innej firmy, tak jakby został wygenerowany dla klienta publicznego.</span><span class="sxs-lookup"><span data-stu-id="02ca4-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="02ca4-541">W przypadku postanowień uwierzytelniania OAuth publiczna aplikacja nie ma tajnego klienta, ponieważ nie może być zaufana do bezpiecznego przechowywania wpisów tajnych, a token dostępu jest generowany dla klienta poufnego.</span><span class="sxs-lookup"><span data-stu-id="02ca4-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="02ca4-542">Klient poufny jest klientem, który ma klucz tajny klienta i ma możliwość bezpiecznego przechowywania wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="02ca4-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="02ca4-543">Token dostępu innej firmy może mieć przyznane dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że firma zewnętrzna emituje token dla bardziej zaufanego klienta.</span><span class="sxs-lookup"><span data-stu-id="02ca4-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="02ca4-544">Podobnie tokeny odświeżania nie powinny być wystawiane dla klienta, który nie jest zaufany. w takim przypadku klient nie będzie miał nieograniczonego dostępu, chyba że zostaną zastosowane inne ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="02ca4-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="02ca4-545">Wykonywanie wywołań interfejsu API z klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="02ca4-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="02ca4-546">Wykonaj wywołanie interfejsu API z klienta do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="02ca4-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="02ca4-547">Na serwerze programu Pobierz token dostępu dla zasobu interfejsu API innej firmy i wystaw, w jaki sposób jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="02ca4-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="02ca4-548">Chociaż to podejście wymaga dodatkowego skoku sieci przez serwer w celu wywołania interfejsu API innej firmy, ostatecznie powoduje bezpieczniejsze środowisko:</span><span class="sxs-lookup"><span data-stu-id="02ca4-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="02ca4-549">Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.</span><span class="sxs-lookup"><span data-stu-id="02ca4-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="02ca4-550">Aplikacja nie może wyciekować tokenów dostępu z serwera, który może zawierać bardziej poufne uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="02ca4-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="02ca4-551">Korzystanie z punktów końcowych programu Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="02ca4-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="02ca4-552">Biblioteka i szablony uwierzytelniania Blazor korzystają z punktów końcowych Open ID Connect (OIDC) w wersji 1.0.</span><span class="sxs-lookup"><span data-stu-id="02ca4-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="02ca4-553">Aby użyć punktu końcowego v 2.0, skonfiguruj opcję okaziciela JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="02ca4-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="02ca4-554">W poniższym przykładzie skonfigurowano usługi AAD dla programu v 2.0 przez dołączenie `v2.0` segmentu do <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="02ca4-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="02ca4-555">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="02ca4-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="02ca4-556">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="02ca4-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="02ca4-557">Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> lub w pliku ustawień aplikacji (*appSettings. JSON*) przy użyciu `Authority` klucza.</span><span class="sxs-lookup"><span data-stu-id="02ca4-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="02ca4-558">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="02ca4-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="02ca4-559">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="02ca4-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
