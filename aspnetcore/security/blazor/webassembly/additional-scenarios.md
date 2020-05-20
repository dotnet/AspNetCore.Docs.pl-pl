---
<span data-ttu-id="9c714-101">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-103">'Blazor'</span></span>
- <span data-ttu-id="9c714-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-104">'Identity'</span></span>
- <span data-ttu-id="9c714-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-106">'Razor'</span></span>
- <span data-ttu-id="9c714-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="9c714-108">BlazorDodatkowe scenariusze zabezpieczeń ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="9c714-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="9c714-109">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="9c714-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="9c714-110">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="9c714-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="9c714-111">`AuthorizationMessageHandler`Usługa może być używana z programem `HttpClient` w celu dołączania tokenów dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="9c714-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="9c714-112">Tokeny są uzyskiwane przy użyciu istniejącej `IAccessTokenProvider` usługi.</span><span class="sxs-lookup"><span data-stu-id="9c714-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="9c714-113">Jeśli nie można uzyskać tokenu, `AccessTokenNotAvailableException` zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="9c714-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="9c714-114">`AccessTokenNotAvailableException`ma `Redirect` metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="9c714-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="9c714-115">`AuthorizationMessageHandler`Można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL przy użyciu `ConfigureHandler` metody.</span><span class="sxs-lookup"><span data-stu-id="9c714-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="9c714-116">W poniższym przykładzie `AuthorizationMessageHandler` konfiguruje `HttpClient` w `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9c714-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="9c714-117">Dla wygody `BaseAddressAuthorizationMessageHandler` jest dołączony wstępnie skonfigurowany przy użyciu adresu podstawowego aplikacji jako autoryzowany adres URL.</span><span class="sxs-lookup"><span data-stu-id="9c714-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="9c714-118">Szablony zestawów webassembly z włączonym uwierzytelnianiem są Blazor teraz używane <xref:System.Net.Http.IHttpClientFactory> w projekcie interfejsu API serwera w celu skonfigurowania programu <xref:System.Net.Http.HttpClient> przy użyciu `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="9c714-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="9c714-119">W przypadku, gdy klient został utworzony za pomocą programu `CreateClient` w powyższym przykładzie, <xref:System.Net.Http.HttpClient> jest dostarczane wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="9c714-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="9c714-120">Skonfigurowany <xref:System.Net.Http.HttpClient> jest następnie używany do autoryzowania żądań przy użyciu prostego `try-catch` wzorca.</span><span class="sxs-lookup"><span data-stu-id="9c714-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="9c714-121">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9c714-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="9c714-122">Wpisane HttpClient</span><span class="sxs-lookup"><span data-stu-id="9c714-122">Typed HttpClient</span></span>

<span data-ttu-id="9c714-123">Można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy.</span><span class="sxs-lookup"><span data-stu-id="9c714-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="9c714-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="9c714-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="9c714-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9c714-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="9c714-126">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9c714-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="9c714-127">Konfigurowanie procedury obsługi HttpClient</span><span class="sxs-lookup"><span data-stu-id="9c714-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="9c714-128">Program obsługi można skonfigurować w taki sposób, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Aby wychodzące żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="9c714-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="9c714-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9c714-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="9c714-130">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="9c714-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="9c714-131">Jeśli Blazor aplikacja internetowa zestawu zwykle używa bezpiecznego ustawienia domyślnego <xref:System.Net.Http.HttpClient> , aplikacja może również wykonać nieuwierzytelnione lub nieautoryzowane żądania internetowego interfejsu API przez skonfigurowanie nazwanego elementu <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="9c714-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="9c714-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9c714-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="9c714-133">Poprzednia rejestracja jest uzupełnieniem istniejącej bezpiecznej rejestracji domyślnej <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="9c714-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="9c714-134">Składnik tworzy z programu, <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> Aby wykonać nieuwierzytelnione lub nieautoryzowane żądania:</span><span class="sxs-lookup"><span data-stu-id="9c714-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="9c714-135">Kontroler w interfejsie API serwera, `WeatherForecastNoAuthenticationController` dla poprzedniego przykładu, nie jest oznaczony [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="9c714-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="9c714-136">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="9c714-136">Request additional access tokens</span></span>

<span data-ttu-id="9c714-137">Tokeny dostępu można uzyskać ręcznie, wywołując metodę `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="9c714-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="9c714-138">W poniższym przykładzie dodatkowe zakresy interfejsu API usługi Azure Active Directory (AAD) Microsoft Graph są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="9c714-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="9c714-139">Po dodaniu Microsoft Graph uprawnień interfejsu API w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="9c714-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="9c714-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9c714-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="9c714-141">`IAccessTokenProvider.RequestToken`Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów.</span><span class="sxs-lookup"><span data-stu-id="9c714-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="9c714-142">W Razor składniku:</span><span class="sxs-lookup"><span data-stu-id="9c714-142">In a Razor component:</span></span>

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

<span data-ttu-id="9c714-143">`TryGetToken`typu</span><span class="sxs-lookup"><span data-stu-id="9c714-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="9c714-144">`true`z `token` do użycia.</span><span class="sxs-lookup"><span data-stu-id="9c714-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="9c714-145">`false`Jeśli nie pobrano tokenu.</span><span class="sxs-lookup"><span data-stu-id="9c714-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="9c714-146">HttpClient i HttpRequestMessage za pomocą opcji żądania interfejsu API pobierania</span><span class="sxs-lookup"><span data-stu-id="9c714-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="9c714-147">W przypadku uruchamiania w zestawie webassembly w Blazor aplikacji Webassembly [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań.</span><span class="sxs-lookup"><span data-stu-id="9c714-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="9c714-148">Na przykład można określić metodę HTTP i nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="9c714-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="9c714-149">Poniższy składnik wysyła `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="9c714-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="9c714-150">Implementacja programu .NET webassembly `HttpClient` używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="9c714-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="9c714-151">Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="9c714-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="9c714-152">Opcje żądania pobrania HTTP można skonfigurować przy użyciu `HttpRequestMessage` metod rozszerzających pokazanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9c714-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="9c714-153">`HttpRequestMessage`Metoda rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="9c714-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="9c714-154">Właściwość żądania pobrania</span><span class="sxs-lookup"><span data-stu-id="9c714-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="9c714-155">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-156">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-157">'Blazor'</span></span>
- <span data-ttu-id="9c714-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-158">'Identity'</span></span>
- <span data-ttu-id="9c714-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-160">'Razor'</span></span>
- <span data-ttu-id="9c714-161">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-162">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-163">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-164">'Blazor'</span></span>
- <span data-ttu-id="9c714-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-165">'Identity'</span></span>
- <span data-ttu-id="9c714-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-167">'Razor'</span></span>
- <span data-ttu-id="9c714-168">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-169">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-170">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-171">'Blazor'</span></span>
- <span data-ttu-id="9c714-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-172">'Identity'</span></span>
- <span data-ttu-id="9c714-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-174">'Razor'</span></span>
- <span data-ttu-id="9c714-175">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-176">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-178">'Blazor'</span></span>
- <span data-ttu-id="9c714-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-179">'Identity'</span></span>
- <span data-ttu-id="9c714-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-181">'Razor'</span></span>
- <span data-ttu-id="9c714-182">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-183">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-185">'Blazor'</span></span>
- <span data-ttu-id="9c714-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-186">'Identity'</span></span>
- <span data-ttu-id="9c714-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-188">'Razor'</span></span>
- <span data-ttu-id="9c714-189">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-190">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-192">'Blazor'</span></span>
- <span data-ttu-id="9c714-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-193">'Identity'</span></span>
- <span data-ttu-id="9c714-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-195">'Razor'</span></span>
- <span data-ttu-id="9c714-196">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-197">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-199">'Blazor'</span></span>
- <span data-ttu-id="9c714-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-200">'Identity'</span></span>
- <span data-ttu-id="9c714-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-202">'Razor'</span></span>
- <span data-ttu-id="9c714-203">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-204">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-205">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-206">'Blazor'</span></span>
- <span data-ttu-id="9c714-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-207">'Identity'</span></span>
- <span data-ttu-id="9c714-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-209">'Razor'</span></span>
- <span data-ttu-id="9c714-210">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-211">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-213">'Blazor'</span></span>
- <span data-ttu-id="9c714-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-214">'Identity'</span></span>
- <span data-ttu-id="9c714-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-216">'Razor'</span></span>
- <span data-ttu-id="9c714-217">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-218">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-220">'Blazor'</span></span>
- <span data-ttu-id="9c714-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-221">'Identity'</span></span>
- <span data-ttu-id="9c714-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-223">'Razor'</span></span>
- <span data-ttu-id="9c714-224">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-225">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-227">'Blazor'</span></span>
- <span data-ttu-id="9c714-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-228">'Identity'</span></span>
- <span data-ttu-id="9c714-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-230">'Razor'</span></span>
- <span data-ttu-id="9c714-231">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-232">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-234">'Blazor'</span></span>
- <span data-ttu-id="9c714-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-235">'Identity'</span></span>
- <span data-ttu-id="9c714-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-237">'Razor'</span></span>
- <span data-ttu-id="9c714-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-239">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-241">'Blazor'</span></span>
- <span data-ttu-id="9c714-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-242">'Identity'</span></span>
- <span data-ttu-id="9c714-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-244">'Razor'</span></span>
- <span data-ttu-id="9c714-245">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-246">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-248">'Blazor'</span></span>
- <span data-ttu-id="9c714-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-249">'Identity'</span></span>
- <span data-ttu-id="9c714-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-251">'Razor'</span></span>
- <span data-ttu-id="9c714-252">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-253">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-254">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-255">'Blazor'</span></span>
- <span data-ttu-id="9c714-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-256">'Identity'</span></span>
- <span data-ttu-id="9c714-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-258">'Razor'</span></span>
- <span data-ttu-id="9c714-259">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-260">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-261">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-262">'Blazor'</span></span>
- <span data-ttu-id="9c714-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-263">'Identity'</span></span>
- <span data-ttu-id="9c714-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-265">'Razor'</span></span>
- <span data-ttu-id="9c714-266">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-266">'SignalR' uid:</span></span> 

<span data-ttu-id="9c714-267">------------------- | ---title: "ASP.NET Core elementu Blazor webassembly dodatkowe scenariusze zabezpieczeń" Author: Description: "informacje o konfigurowaniu Blazor zestawu webassembly w celu uzyskania dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-268">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-269">'Blazor'</span></span>
- <span data-ttu-id="9c714-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-270">'Identity'</span></span>
- <span data-ttu-id="9c714-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-272">'Razor'</span></span>
- <span data-ttu-id="9c714-273">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-274">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-275">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-276">'Blazor'</span></span>
- <span data-ttu-id="9c714-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-277">'Identity'</span></span>
- <span data-ttu-id="9c714-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-279">'Razor'</span></span>
- <span data-ttu-id="9c714-280">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-281">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-282">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-283">'Blazor'</span></span>
- <span data-ttu-id="9c714-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-284">'Identity'</span></span>
- <span data-ttu-id="9c714-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-286">'Razor'</span></span>
- <span data-ttu-id="9c714-287">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-288">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-290">'Blazor'</span></span>
- <span data-ttu-id="9c714-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-291">'Identity'</span></span>
- <span data-ttu-id="9c714-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-293">'Razor'</span></span>
- <span data-ttu-id="9c714-294">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-295">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-297">'Blazor'</span></span>
- <span data-ttu-id="9c714-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-298">'Identity'</span></span>
- <span data-ttu-id="9c714-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-300">'Razor'</span></span>
- <span data-ttu-id="9c714-301">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-302">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-304">'Blazor'</span></span>
- <span data-ttu-id="9c714-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-305">'Identity'</span></span>
- <span data-ttu-id="9c714-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-307">'Razor'</span></span>
- <span data-ttu-id="9c714-308">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-309">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-310">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-311">'Blazor'</span></span>
- <span data-ttu-id="9c714-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-312">'Identity'</span></span>
- <span data-ttu-id="9c714-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-314">'Razor'</span></span>
- <span data-ttu-id="9c714-315">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-316">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-317">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-318">'Blazor'</span></span>
- <span data-ttu-id="9c714-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-319">'Identity'</span></span>
- <span data-ttu-id="9c714-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-321">'Razor'</span></span>
- <span data-ttu-id="9c714-322">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-323">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-324">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-325">'Blazor'</span></span>
- <span data-ttu-id="9c714-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-326">'Identity'</span></span>
- <span data-ttu-id="9c714-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-328">'Razor'</span></span>
- <span data-ttu-id="9c714-329">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-329">'SignalR' uid:</span></span> 

<span data-ttu-id="9c714-330">----------- | | `SetBrowserRequestCredentials`         |  [poświadczenia](https://developer.mozilla.org/docs/Web/API/Request/credentials) | `SetBrowserRequestCache` |               |  [pamięć podręczna](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [tryb](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [integralność](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="9c714-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="9c714-331">Można ustawić dodatkowe opcje przy użyciu bardziej generycznej `SetBrowserRequestOption` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="9c714-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="9c714-332">Odpowiedź HTTP jest zwykle buforowana w Blazor aplikacji Webassembly, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9c714-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="9c714-333">Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy użyć `SetBrowserResponseStreamingEnabled` metody rozszerzenia w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="9c714-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="9c714-334">Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj `SetBrowserRequestCredentials` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="9c714-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="9c714-335">Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="9c714-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="9c714-336">Podczas wysyłania poświadczeń (plików cookie/nagłówki autoryzacji) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="9c714-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="9c714-337">Następujące zasady obejmują konfigurację programu:</span><span class="sxs-lookup"><span data-stu-id="9c714-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="9c714-338">Pochodzenie żądania ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="9c714-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="9c714-339">Dowolna Metoda (czasownik).</span><span class="sxs-lookup"><span data-stu-id="9c714-339">Any method (verb).</span></span>
* <span data-ttu-id="9c714-340">`Content-Type`i `Authorization` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="9c714-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="9c714-341">Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header` ), Wyświetl nagłówek podczas wywoływania <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="9c714-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="9c714-342">Poświadczenia ustawione przez kod JavaScript po stronie klienta ( `credentials` Właściwość ustawiona na `include` ).</span><span class="sxs-lookup"><span data-stu-id="9c714-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="9c714-343">Aby uzyskać więcej informacji, zobacz <xref:security/cors> i składnik Tester żądania HTTP aplikacji przykładowej (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="9c714-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="9c714-344">Obsługa błędów żądania tokenu</span><span class="sxs-lookup"><span data-stu-id="9c714-344">Handle token request errors</span></span>

<span data-ttu-id="9c714-345">Gdy aplikacja jednostronicowa uwierzytelnia użytkownika przy użyciu funkcji Open ID Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w Identity postaci pliku cookie sesji, który jest ustawiany w wyniku użytkownika dostarczającego poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="9c714-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="9c714-346">Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny.</span><span class="sxs-lookup"><span data-stu-id="9c714-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="9c714-347">W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów.</span><span class="sxs-lookup"><span data-stu-id="9c714-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="9c714-348">W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.</span><span class="sxs-lookup"><span data-stu-id="9c714-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="9c714-349">Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP.</span><span class="sxs-lookup"><span data-stu-id="9c714-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="9c714-350">Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="9c714-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="9c714-351">Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.</span><span class="sxs-lookup"><span data-stu-id="9c714-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="9c714-352">Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach.</span><span class="sxs-lookup"><span data-stu-id="9c714-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="9c714-353">Są one częścią charakteru aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="9c714-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="9c714-354">SPA używający plików cookie również nie może wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="9c714-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="9c714-355">Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:</span><span class="sxs-lookup"><span data-stu-id="9c714-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="9c714-356">Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9c714-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="9c714-357">Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9c714-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="9c714-358">Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:</span><span class="sxs-lookup"><span data-stu-id="9c714-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="9c714-359">Żądanie powiodło się, a aplikacja ma prawidłowy token.</span><span class="sxs-lookup"><span data-stu-id="9c714-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="9c714-360">Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="9c714-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="9c714-361">Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="9c714-362">Istnieją różne podejścia z rosnącymi poziomami złożoności:</span><span class="sxs-lookup"><span data-stu-id="9c714-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="9c714-363">Przechowuj bieżący stan strony w magazynie sesji.</span><span class="sxs-lookup"><span data-stu-id="9c714-363">Store the current page state in session storage.</span></span> <span data-ttu-id="9c714-364">`OnInitializeAsync`Przed kontynuowaniem sprawdź, czy stan można przywrócić.</span><span class="sxs-lookup"><span data-stu-id="9c714-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="9c714-365">Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.</span><span class="sxs-lookup"><span data-stu-id="9c714-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="9c714-366">Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.</span><span class="sxs-lookup"><span data-stu-id="9c714-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="9c714-367">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="9c714-367">The following example shows how to:</span></span>

* <span data-ttu-id="9c714-368">Zachowaj stan przed przekierowaniem do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="9c714-369">Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="9c714-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="9c714-370">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9c714-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="9c714-371">Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP.</span><span class="sxs-lookup"><span data-stu-id="9c714-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="9c714-372">Taka sytuacja może wystąpić w przypadku korzystania z takiego elementu jak kontenera stanu i przywrócenia stanu po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="9c714-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="9c714-373">Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9c714-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="9c714-374">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9c714-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="9c714-375">Dostosowywanie tras aplikacji</span><span class="sxs-lookup"><span data-stu-id="9c714-375">Customize app routes</span></span>

<span data-ttu-id="9c714-376">Domyślnie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Biblioteka używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9c714-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="9c714-377">Trasa</span><span class="sxs-lookup"><span data-stu-id="9c714-377">Route</span></span>                            | <span data-ttu-id="9c714-378">Przeznaczenie</span><span class="sxs-lookup"><span data-stu-id="9c714-378">Purpose</span></span> |
| ---
<span data-ttu-id="9c714-379">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-380">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-381">'Blazor'</span></span>
- <span data-ttu-id="9c714-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-382">'Identity'</span></span>
- <span data-ttu-id="9c714-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-384">'Razor'</span></span>
- <span data-ttu-id="9c714-385">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-386">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-387">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-388">'Blazor'</span></span>
- <span data-ttu-id="9c714-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-389">'Identity'</span></span>
- <span data-ttu-id="9c714-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-391">'Razor'</span></span>
- <span data-ttu-id="9c714-392">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-393">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-394">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-395">'Blazor'</span></span>
- <span data-ttu-id="9c714-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-396">'Identity'</span></span>
- <span data-ttu-id="9c714-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-398">'Razor'</span></span>
- <span data-ttu-id="9c714-399">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-400">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-401">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-402">'Blazor'</span></span>
- <span data-ttu-id="9c714-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-403">'Identity'</span></span>
- <span data-ttu-id="9c714-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-405">'Razor'</span></span>
- <span data-ttu-id="9c714-406">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-407">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-408">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-409">'Blazor'</span></span>
- <span data-ttu-id="9c714-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-410">'Identity'</span></span>
- <span data-ttu-id="9c714-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-412">'Razor'</span></span>
- <span data-ttu-id="9c714-413">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-414">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-415">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-416">'Blazor'</span></span>
- <span data-ttu-id="9c714-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-417">'Identity'</span></span>
- <span data-ttu-id="9c714-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-419">'Razor'</span></span>
- <span data-ttu-id="9c714-420">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-421">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-422">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-423">'Blazor'</span></span>
- <span data-ttu-id="9c714-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-424">'Identity'</span></span>
- <span data-ttu-id="9c714-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-426">'Razor'</span></span>
- <span data-ttu-id="9c714-427">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-428">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-429">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-430">'Blazor'</span></span>
- <span data-ttu-id="9c714-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-431">'Identity'</span></span>
- <span data-ttu-id="9c714-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-433">'Razor'</span></span>
- <span data-ttu-id="9c714-434">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-435">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-436">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-437">'Blazor'</span></span>
- <span data-ttu-id="9c714-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-438">'Identity'</span></span>
- <span data-ttu-id="9c714-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-440">'Razor'</span></span>
- <span data-ttu-id="9c714-441">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-442">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-443">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-444">'Blazor'</span></span>
- <span data-ttu-id="9c714-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-445">'Identity'</span></span>
- <span data-ttu-id="9c714-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-447">'Razor'</span></span>
- <span data-ttu-id="9c714-448">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-449">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-450">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-451">'Blazor'</span></span>
- <span data-ttu-id="9c714-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-452">'Identity'</span></span>
- <span data-ttu-id="9c714-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-454">'Razor'</span></span>
- <span data-ttu-id="9c714-455">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-456">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-457">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-458">'Blazor'</span></span>
- <span data-ttu-id="9c714-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-459">'Identity'</span></span>
- <span data-ttu-id="9c714-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-461">'Razor'</span></span>
- <span data-ttu-id="9c714-462">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-463">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-464">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-465">'Blazor'</span></span>
- <span data-ttu-id="9c714-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-466">'Identity'</span></span>
- <span data-ttu-id="9c714-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-468">'Razor'</span></span>
- <span data-ttu-id="9c714-469">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-470">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-471">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-472">'Blazor'</span></span>
- <span data-ttu-id="9c714-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-473">'Identity'</span></span>
- <span data-ttu-id="9c714-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-475">'Razor'</span></span>
- <span data-ttu-id="9c714-476">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-476">'SignalR' uid:</span></span> 

<span data-ttu-id="9c714-477">---------------- | ---title: "ASP.NET Core elementu Blazor webassembly dodatkowe scenariusze zabezpieczeń" Author: Description: "informacje o konfigurowaniu Blazor zestawu webassembly w celu uzyskania dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-478">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-479">'Blazor'</span></span>
- <span data-ttu-id="9c714-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-480">'Identity'</span></span>
- <span data-ttu-id="9c714-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-482">'Razor'</span></span>
- <span data-ttu-id="9c714-483">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-483">'SignalR' uid:</span></span> 

<span data-ttu-id="9c714-484">---- | | `authentication/login`           | Wyzwala operację logowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="9c714-485">| | `authentication/login-callback`  | Obsługuje wynik operacji logowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="9c714-486">| | `authentication/login-failed`    | Wyświetla komunikaty o błędach, gdy operacja logowania zakończy się niepowodzeniem z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="9c714-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="9c714-487">| | `authentication/logout`          | Wyzwala operację wylogowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="9c714-488">| | `authentication/logout-callback` | Obsługuje wynik operacji wylogowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="9c714-489">| | `authentication/logout-failed`   | Wyświetla komunikaty o błędach, gdy operacja wylogowania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="9c714-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="9c714-490">| | `authentication/logged-out`      | Wskazuje, że użytkownik pomyślnie wylogować się.</span><span class="sxs-lookup"><span data-stu-id="9c714-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="9c714-491">| | `authentication/profile`         | Wyzwala operację edytowania profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9c714-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="9c714-492">| | `authentication/register`        | Wyzwala operację w celu zarejestrowania nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9c714-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="9c714-493">Trasy przedstawione w powyższej tabeli można konfigurować za pośrednictwem programu `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="9c714-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="9c714-494">Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="9c714-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="9c714-495">W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security` .</span><span class="sxs-lookup"><span data-stu-id="9c714-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="9c714-496">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9c714-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="9c714-497">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9c714-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="9c714-498">Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować `RemoteAuthenticatorView` parametr z jawnym akcją:</span><span class="sxs-lookup"><span data-stu-id="9c714-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="9c714-499">Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="9c714-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="9c714-500">Dostosowywanie interfejsu użytkownika uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9c714-500">Customize the authentication user interface</span></span>

<span data-ttu-id="9c714-501">`RemoteAuthenticatorView`zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9c714-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="9c714-502">Każdy stan można dostosować, przekazując element niestandardowe `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="9c714-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="9c714-503">Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w `RemoteAuthenticatorView` następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="9c714-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="9c714-504">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9c714-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="9c714-505">`RemoteAuthenticatorView`Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9c714-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="9c714-506">Trasa</span><span class="sxs-lookup"><span data-stu-id="9c714-506">Route</span></span>                            | <span data-ttu-id="9c714-507">Fragment</span><span class="sxs-lookup"><span data-stu-id="9c714-507">Fragment</span></span>                |
| ---
<span data-ttu-id="9c714-508">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-509">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-510">'Blazor'</span></span>
- <span data-ttu-id="9c714-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-511">'Identity'</span></span>
- <span data-ttu-id="9c714-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-513">'Razor'</span></span>
- <span data-ttu-id="9c714-514">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-515">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-516">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-517">'Blazor'</span></span>
- <span data-ttu-id="9c714-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-518">'Identity'</span></span>
- <span data-ttu-id="9c714-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-520">'Razor'</span></span>
- <span data-ttu-id="9c714-521">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-522">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-523">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-524">'Blazor'</span></span>
- <span data-ttu-id="9c714-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-525">'Identity'</span></span>
- <span data-ttu-id="9c714-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-527">'Razor'</span></span>
- <span data-ttu-id="9c714-528">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-529">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-530">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-531">'Blazor'</span></span>
- <span data-ttu-id="9c714-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-532">'Identity'</span></span>
- <span data-ttu-id="9c714-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-534">'Razor'</span></span>
- <span data-ttu-id="9c714-535">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-536">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-537">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-538">'Blazor'</span></span>
- <span data-ttu-id="9c714-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-539">'Identity'</span></span>
- <span data-ttu-id="9c714-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-541">'Razor'</span></span>
- <span data-ttu-id="9c714-542">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-543">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-544">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-545">'Blazor'</span></span>
- <span data-ttu-id="9c714-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-546">'Identity'</span></span>
- <span data-ttu-id="9c714-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-548">'Razor'</span></span>
- <span data-ttu-id="9c714-549">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-550">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-551">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-552">'Blazor'</span></span>
- <span data-ttu-id="9c714-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-553">'Identity'</span></span>
- <span data-ttu-id="9c714-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-555">'Razor'</span></span>
- <span data-ttu-id="9c714-556">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-557">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-558">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-559">'Blazor'</span></span>
- <span data-ttu-id="9c714-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-560">'Identity'</span></span>
- <span data-ttu-id="9c714-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-562">'Razor'</span></span>
- <span data-ttu-id="9c714-563">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-564">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-565">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-566">'Blazor'</span></span>
- <span data-ttu-id="9c714-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-567">'Identity'</span></span>
- <span data-ttu-id="9c714-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-569">'Razor'</span></span>
- <span data-ttu-id="9c714-570">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-571">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-572">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-573">'Blazor'</span></span>
- <span data-ttu-id="9c714-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-574">'Identity'</span></span>
- <span data-ttu-id="9c714-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-576">'Razor'</span></span>
- <span data-ttu-id="9c714-577">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-578">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-579">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-580">'Blazor'</span></span>
- <span data-ttu-id="9c714-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-581">'Identity'</span></span>
- <span data-ttu-id="9c714-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-583">'Razor'</span></span>
- <span data-ttu-id="9c714-584">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-585">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-586">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-587">'Blazor'</span></span>
- <span data-ttu-id="9c714-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-588">'Identity'</span></span>
- <span data-ttu-id="9c714-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-590">'Razor'</span></span>
- <span data-ttu-id="9c714-591">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-592">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-593">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-594">'Blazor'</span></span>
- <span data-ttu-id="9c714-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-595">'Identity'</span></span>
- <span data-ttu-id="9c714-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-597">'Razor'</span></span>
- <span data-ttu-id="9c714-598">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-599">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-600">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-601">'Blazor'</span></span>
- <span data-ttu-id="9c714-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-602">'Identity'</span></span>
- <span data-ttu-id="9c714-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-604">'Razor'</span></span>
- <span data-ttu-id="9c714-605">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-605">'SignalR' uid:</span></span> 

<span data-ttu-id="9c714-606">---------------- | ---title: "ASP.NET Core elementu Blazor webassembly dodatkowe scenariusze zabezpieczeń" Author: Description: "informacje o konfigurowaniu Blazor zestawu webassembly w celu uzyskania dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-607">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-608">'Blazor'</span></span>
- <span data-ttu-id="9c714-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-609">'Identity'</span></span>
- <span data-ttu-id="9c714-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-611">'Razor'</span></span>
- <span data-ttu-id="9c714-612">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-613">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-614">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-615">'Blazor'</span></span>
- <span data-ttu-id="9c714-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-616">'Identity'</span></span>
- <span data-ttu-id="9c714-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-618">'Razor'</span></span>
- <span data-ttu-id="9c714-619">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-620">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-621">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-622">'Blazor'</span></span>
- <span data-ttu-id="9c714-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-623">'Identity'</span></span>
- <span data-ttu-id="9c714-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-625">'Razor'</span></span>
- <span data-ttu-id="9c714-626">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-627">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-628">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-629">'Blazor'</span></span>
- <span data-ttu-id="9c714-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-630">'Identity'</span></span>
- <span data-ttu-id="9c714-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-632">'Razor'</span></span>
- <span data-ttu-id="9c714-633">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-634">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-635">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-636">'Blazor'</span></span>
- <span data-ttu-id="9c714-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-637">'Identity'</span></span>
- <span data-ttu-id="9c714-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-639">'Razor'</span></span>
- <span data-ttu-id="9c714-640">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-641">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-642">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-643">'Blazor'</span></span>
- <span data-ttu-id="9c714-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-644">'Identity'</span></span>
- <span data-ttu-id="9c714-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-646">'Razor'</span></span>
- <span data-ttu-id="9c714-647">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-648">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-649">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-650">'Blazor'</span></span>
- <span data-ttu-id="9c714-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-651">'Identity'</span></span>
- <span data-ttu-id="9c714-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-653">'Razor'</span></span>
- <span data-ttu-id="9c714-654">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-655">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-656">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-657">'Blazor'</span></span>
- <span data-ttu-id="9c714-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-658">'Identity'</span></span>
- <span data-ttu-id="9c714-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-660">'Razor'</span></span>
- <span data-ttu-id="9c714-661">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9c714-662">title: "ASP.NET Core Blazor webassembly — dodatkowe scenariusze zabezpieczeń" autor: Opis: "Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń".</span><span class="sxs-lookup"><span data-stu-id="9c714-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="9c714-663">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9c714-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c714-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c714-664">'Blazor'</span></span>
- <span data-ttu-id="9c714-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c714-665">'Identity'</span></span>
- <span data-ttu-id="9c714-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c714-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c714-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c714-667">'Razor'</span></span>
- <span data-ttu-id="9c714-668">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9c714-668">'SignalR' uid:</span></span> 

<span data-ttu-id="9c714-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="9c714-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="9c714-670">Dostosowywanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="9c714-670">Customize the user</span></span>

<span data-ttu-id="9c714-671">Użytkownicy powiązana z aplikacją mogą być dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="9c714-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="9c714-672">W poniższym przykładzie Wszyscy uwierzytelnieni użytkownicy otrzymują `amr` każde z metod uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9c714-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="9c714-673">Utwórz klasę rozszerzającą `RemoteUserAccount` klasę:</span><span class="sxs-lookup"><span data-stu-id="9c714-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="9c714-674">Utwórz fabrykę, która rozszerza `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="9c714-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="9c714-675">Zarejestruj `CustomAccountFactory` dostawcę uwierzytelniania w użyciu.</span><span class="sxs-lookup"><span data-stu-id="9c714-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="9c714-676">Wszystkie następujące rejestracje są prawidłowe:</span><span class="sxs-lookup"><span data-stu-id="9c714-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="9c714-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9c714-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="9c714-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9c714-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="9c714-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="9c714-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="9c714-680">Obsługa prerenderowania przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9c714-680">Support prerendering with authentication</span></span>

<span data-ttu-id="9c714-681">Po zastosowaniu wskazówek w jednej z aplikacji hostowanego Blazor zestawu webassembly Skorzystaj z poniższych instrukcji, aby utworzyć aplikację, która:</span><span class="sxs-lookup"><span data-stu-id="9c714-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="9c714-682">Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="9c714-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="9c714-683">Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="9c714-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="9c714-684">W klasie aplikacji klienckiej `Program` (*program.cs*) należy wykonać typowe rejestracje usługi w oddzielnym metodzie (na przykład `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="9c714-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="9c714-685">W aplikacji serwera `Startup.ConfigureServices` zarejestruj następujące dodatkowe usługi:</span><span class="sxs-lookup"><span data-stu-id="9c714-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="9c714-686">W metodzie aplikacji serwera `Startup.Configure` Zastąp ciąg `endpoints.MapFallbackToFile("index.html")` opcją `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="9c714-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="9c714-687">W aplikacji serwer Utwórz folder *strony* , jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="9c714-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="9c714-688">Utwórz stronę *_Host. cshtml* w folderze *strony* aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="9c714-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="9c714-689">Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9c714-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="9c714-690">Aktualizuj zawartość pliku:</span><span class="sxs-lookup"><span data-stu-id="9c714-690">Update the file's contents:</span></span>

* <span data-ttu-id="9c714-691">Dodaj `@page "_Host"` na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="9c714-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="9c714-692">Zastąp `<app>Loading...</app>` tag następującym:</span><span class="sxs-lookup"><span data-stu-id="9c714-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="9c714-693">Opcje aplikacji hostowanych i dostawców logowania innych firm</span><span class="sxs-lookup"><span data-stu-id="9c714-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="9c714-694">Podczas uwierzytelniania i autoryzowania hostowanej Blazor aplikacji sieci webassembly przy użyciu dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9c714-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="9c714-695">Wybór jednego z nich zależy od danego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="9c714-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="9c714-696">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="9c714-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="9c714-697">Uwierzytelnianie użytkowników tylko w celu wywołania chronionych interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="9c714-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="9c714-698">Uwierzytelnij użytkownika za pomocą przepływu OAuth po stronie klienta dla dostawcy interfejsu API innej firmy:</span><span class="sxs-lookup"><span data-stu-id="9c714-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="9c714-699">W tym scenariuszu:</span><span class="sxs-lookup"><span data-stu-id="9c714-699">In this scenario:</span></span>

* <span data-ttu-id="9c714-700">Serwer hostujący aplikację nie odgrywa roli.</span><span class="sxs-lookup"><span data-stu-id="9c714-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="9c714-701">Nie można chronić interfejsów API na serwerze.</span><span class="sxs-lookup"><span data-stu-id="9c714-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="9c714-702">Aplikacja może wywoływać tylko chronione interfejsy API innych firm.</span><span class="sxs-lookup"><span data-stu-id="9c714-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="9c714-703">Uwierzytelnianie użytkowników za pomocą dostawcy innych firm i wywoływanie chronionych interfejsów API na serwerze hosta i stronie trzeciej</span><span class="sxs-lookup"><span data-stu-id="9c714-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="9c714-704">Skonfiguruj Identity przy użyciu innego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="9c714-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="9c714-705">Uzyskaj tokeny wymagane przez dostęp do interfejsu API innych firm i Zapisz je.</span><span class="sxs-lookup"><span data-stu-id="9c714-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="9c714-706">Gdy użytkownik loguje się, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9c714-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="9c714-707">W tym momencie istnieje kilka podejścia dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.</span><span class="sxs-lookup"><span data-stu-id="9c714-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="9c714-708">Korzystanie z tokenu dostępu do serwera w celu pobrania tokenu dostępu innej firmy</span><span class="sxs-lookup"><span data-stu-id="9c714-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="9c714-709">Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="9c714-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="9c714-710">Z tego miejsca Użyj tokenu dostępu innej firmy do wywołania zasobów interfejsu API innych firm bezpośrednio z poziomu Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="9c714-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="9c714-711">Nie zalecamy tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="9c714-711">We don't recommend this approach.</span></span> <span data-ttu-id="9c714-712">Takie podejście wymaga traktowania tokenu dostępu innej firmy, tak jakby został wygenerowany dla klienta publicznego.</span><span class="sxs-lookup"><span data-stu-id="9c714-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="9c714-713">W przypadku postanowień uwierzytelniania OAuth publiczna aplikacja nie ma tajnego klienta, ponieważ nie może być zaufana do bezpiecznego przechowywania wpisów tajnych, a token dostępu jest generowany dla klienta poufnego.</span><span class="sxs-lookup"><span data-stu-id="9c714-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="9c714-714">Klient poufny jest klientem, który ma klucz tajny klienta i ma możliwość bezpiecznego przechowywania wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="9c714-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="9c714-715">Token dostępu innej firmy może mieć przyznane dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że firma zewnętrzna emituje token dla bardziej zaufanego klienta.</span><span class="sxs-lookup"><span data-stu-id="9c714-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="9c714-716">Podobnie tokeny odświeżania nie powinny być wystawiane dla klienta, który nie jest zaufany. w takim przypadku klient nie będzie miał nieograniczonego dostępu, chyba że zostaną zastosowane inne ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="9c714-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="9c714-717">Wykonywanie wywołań interfejsu API z klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="9c714-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="9c714-718">Wykonaj wywołanie interfejsu API z klienta do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="9c714-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="9c714-719">Na serwerze programu Pobierz token dostępu dla zasobu interfejsu API innej firmy i wystaw, w jaki sposób jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="9c714-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="9c714-720">Chociaż to podejście wymaga dodatkowego skoku sieci przez serwer w celu wywołania interfejsu API innej firmy, ostatecznie powoduje bezpieczniejsze środowisko:</span><span class="sxs-lookup"><span data-stu-id="9c714-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="9c714-721">Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.</span><span class="sxs-lookup"><span data-stu-id="9c714-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="9c714-722">Aplikacja nie może wyciekować tokenów dostępu z serwera, który może zawierać bardziej poufne uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="9c714-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="9c714-723">Korzystanie z punktów końcowych programu Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="9c714-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="9c714-724">Biblioteka i szablony uwierzytelniania Blazor korzystają z punktów końcowych Open ID Connect (OIDC) w wersji 1.0.</span><span class="sxs-lookup"><span data-stu-id="9c714-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="9c714-725">Aby użyć punktu końcowego v 2.0, skonfiguruj opcję okaziciela JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="9c714-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="9c714-726">W poniższym przykładzie skonfigurowano usługi AAD dla programu v 2.0 przez dołączenie `v2.0` segmentu do `Authority` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="9c714-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="9c714-727">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="9c714-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="9c714-728">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw `Authority` Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="9c714-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="9c714-729">Ustaw właściwość w `JwtBearerOptions` lub w pliku ustawień aplikacji na `Authority` klucz.</span><span class="sxs-lookup"><span data-stu-id="9c714-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="9c714-730">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="9c714-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="9c714-731">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="9c714-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
