---
title: BlazorDodatkowe scenariusze zabezpieczeń ASP.NET Core Webassembly
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: de752eb180767bbb269107ebc478a4422448f968
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272037"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="66796-103">BlazorDodatkowe scenariusze zabezpieczeń ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="66796-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="66796-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66796-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="66796-105">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="66796-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="66796-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Usługa może być używana z programem <xref:System.Net.Http.HttpClient> w celu dołączania tokenów dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="66796-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="66796-107">Tokeny są uzyskiwane przy użyciu istniejącej <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> usługi.</span><span class="sxs-lookup"><span data-stu-id="66796-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="66796-108">Jeśli nie można uzyskać tokenu, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="66796-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="66796-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>ma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="66796-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="66796-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="66796-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="66796-111">Użyj jednej z poniższych metod, aby skonfigurować obsługę komunikatów dla żądań wychodzących:</span><span class="sxs-lookup"><span data-stu-id="66796-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="66796-112">[Niestandardowa Klasa AuthorizationMessageHandler](#custom-authorizationmessagehandler-class) (*zalecane*)</span><span class="sxs-lookup"><span data-stu-id="66796-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="66796-113">Konfigurowanie AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="66796-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="66796-114">Niestandardowa Klasa AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="66796-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="66796-115">W poniższym przykładzie Klasa niestandardowa rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , która może służyć do konfigurowania <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="66796-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="66796-116">W programie `Program.Main` (*program.cs*) <xref:System.Net.Http.HttpClient> jest skonfigurowany za pomocą niestandardowego programu obsługi komunikatów autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="66796-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="66796-117">Skonfigurowany <xref:System.Net.Http.HttpClient> jest używany do autoryzacji żądań za pomocą wzorca [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="66796-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="66796-118">Gdy klient jest tworzony przy użyciu <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> [programu (Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Package), <xref:System.Net.Http.HttpClient> są dostarczane wystąpienia, które obejmują tokeny dostępu podczas wykonywania żądań do interfejsu API serwera:</span><span class="sxs-lookup"><span data-stu-id="66796-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="66796-119">Konfigurowanie AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="66796-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="66796-120">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> konfiguruje <xref:System.Net.Http.HttpClient> w `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="66796-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="66796-121">Dla wygody <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> jest dołączony wstępnie skonfigurowany przy użyciu adresu podstawowego aplikacji jako autoryzowany adres URL.</span><span class="sxs-lookup"><span data-stu-id="66796-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="66796-122">Szablony zestawów webassembly z włączonymi uwierzytelnianiem Blazor teraz używają <xref:System.Net.Http.IHttpClientFactory> ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Package) w projekcie interfejsu API serwera w celu skonfigurowania programu <xref:System.Net.Http.HttpClient> przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="66796-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="66796-123">W przypadku, gdy klient został utworzony za pomocą programu <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> w powyższym przykładzie, <xref:System.Net.Http.HttpClient> jest dostarczane wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="66796-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="66796-124">Skonfigurowany <xref:System.Net.Http.HttpClient> jest używany do autoryzacji żądań za pomocą wzorca [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="66796-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="66796-125">Wpisane HttpClient</span><span class="sxs-lookup"><span data-stu-id="66796-125">Typed HttpClient</span></span>

<span data-ttu-id="66796-126">Można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy.</span><span class="sxs-lookup"><span data-stu-id="66796-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="66796-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="66796-127">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="66796-128">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="66796-128">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="66796-129">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="66796-129">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="66796-130">Konfigurowanie procedury obsługi HttpClient</span><span class="sxs-lookup"><span data-stu-id="66796-130">Configure the HttpClient handler</span></span>

<span data-ttu-id="66796-131">Program obsługi można skonfigurować w taki sposób, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Aby wychodzące żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="66796-131">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="66796-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="66796-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="66796-133">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="66796-133">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="66796-134">Jeśli Blazor aplikacja internetowa zestawu zwykle używa bezpiecznego ustawienia domyślnego <xref:System.Net.Http.HttpClient> , aplikacja może również wykonać nieuwierzytelnione lub nieautoryzowane żądania internetowego interfejsu API przez skonfigurowanie nazwanego elementu <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="66796-134">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="66796-135">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="66796-135">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="66796-136">Poprzednia rejestracja jest uzupełnieniem istniejącej bezpiecznej rejestracji domyślnej <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="66796-136">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="66796-137">Składnik tworzy <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> pakietu ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), aby wykonywać nieuwierzytelnione lub nieautoryzowane żądania:</span><span class="sxs-lookup"><span data-stu-id="66796-137">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="66796-138">Kontroler w interfejsie API serwera, `WeatherForecastNoAuthenticationController` dla poprzedniego przykładu, nie jest oznaczony [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="66796-138">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="66796-139">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="66796-139">Request additional access tokens</span></span>

<span data-ttu-id="66796-140">Tokeny dostępu można uzyskać ręcznie, wywołując metodę `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="66796-140">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="66796-141">W poniższym przykładzie dodatkowe zakresy interfejsu API usługi Azure Active Directory (AAD) Microsoft Graph są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="66796-141">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="66796-142">Po dodaniu Microsoft Graph uprawnień interfejsu API w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="66796-142">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="66796-143">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="66796-143">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="66796-144">`IAccessTokenProvider.RequestToken`Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów.</span><span class="sxs-lookup"><span data-stu-id="66796-144">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="66796-145">W Razor składniku:</span><span class="sxs-lookup"><span data-stu-id="66796-145">In a Razor component:</span></span>

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

<span data-ttu-id="66796-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>typu</span><span class="sxs-lookup"><span data-stu-id="66796-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="66796-147">`true`z `token` do użycia.</span><span class="sxs-lookup"><span data-stu-id="66796-147">`true` with the `token` for use.</span></span>
* <span data-ttu-id="66796-148">`false`Jeśli nie pobrano tokenu.</span><span class="sxs-lookup"><span data-stu-id="66796-148">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="66796-149">HttpClient i HttpRequestMessage za pomocą opcji żądania interfejsu API pobierania</span><span class="sxs-lookup"><span data-stu-id="66796-149">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="66796-150">W przypadku uruchamiania w zestawie webassembly w Blazor aplikacji Webassembly [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań.</span><span class="sxs-lookup"><span data-stu-id="66796-150">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="66796-151">Na przykład można określić metodę HTTP i nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="66796-151">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="66796-152">Poniższy składnik wysyła `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="66796-152">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="66796-153">Implementacja programu .NET webassembly <xref:System.Net.Http.HttpClient> używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="66796-153">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="66796-154">Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="66796-154">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="66796-155">Opcje żądania pobrania HTTP można skonfigurować przy użyciu <xref:System.Net.Http.HttpRequestMessage> metod rozszerzających pokazanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="66796-155">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="66796-156">Metoda rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="66796-156">Extension method</span></span> | <span data-ttu-id="66796-157">Właściwość żądania pobrania</span><span class="sxs-lookup"><span data-stu-id="66796-157">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="66796-158">uwierzytelniające</span><span class="sxs-lookup"><span data-stu-id="66796-158">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="66796-159">Chow</span><span class="sxs-lookup"><span data-stu-id="66796-159">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="66796-160">wyst</span><span class="sxs-lookup"><span data-stu-id="66796-160">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="66796-161">spójn</span><span class="sxs-lookup"><span data-stu-id="66796-161">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="66796-162">Można ustawić dodatkowe opcje przy użyciu bardziej generycznej <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="66796-162">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="66796-163">Odpowiedź HTTP jest zwykle buforowana w Blazor aplikacji Webassembly, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="66796-163">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="66796-164">Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy użyć <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metody rozszerzenia w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="66796-164">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="66796-165">Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="66796-165">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="66796-166">Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="66796-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="66796-167">Podczas wysyłania poświadczeń (plików cookie/nagłówki autoryzacji) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="66796-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="66796-168">Następujące zasady obejmują konfigurację programu:</span><span class="sxs-lookup"><span data-stu-id="66796-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="66796-169">Pochodzenie żądania ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="66796-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="66796-170">Dowolna Metoda (czasownik).</span><span class="sxs-lookup"><span data-stu-id="66796-170">Any method (verb).</span></span>
* <span data-ttu-id="66796-171">`Content-Type`i `Authorization` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="66796-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="66796-172">Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header` ), Wyświetl nagłówek podczas wywoływania <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="66796-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="66796-173">Poświadczenia ustawione przez kod JavaScript po stronie klienta ( `credentials` Właściwość ustawiona na `include` ).</span><span class="sxs-lookup"><span data-stu-id="66796-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="66796-174">Aby uzyskać więcej informacji, zobacz <xref:security/cors> i składnik Tester żądania HTTP aplikacji przykładowej (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="66796-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="66796-175">Obsługa błędów żądania tokenu</span><span class="sxs-lookup"><span data-stu-id="66796-175">Handle token request errors</span></span>

<span data-ttu-id="66796-176">Gdy aplikacja jednostronicowa uwierzytelnia użytkownika przy użyciu funkcji Open ID Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w Identity postaci pliku cookie sesji, który jest ustawiany w wyniku użytkownika dostarczającego poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="66796-176">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="66796-177">Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny.</span><span class="sxs-lookup"><span data-stu-id="66796-177">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="66796-178">W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów.</span><span class="sxs-lookup"><span data-stu-id="66796-178">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="66796-179">W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.</span><span class="sxs-lookup"><span data-stu-id="66796-179">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="66796-180">Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP.</span><span class="sxs-lookup"><span data-stu-id="66796-180">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="66796-181">Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="66796-181">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="66796-182">Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.</span><span class="sxs-lookup"><span data-stu-id="66796-182">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="66796-183">Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach.</span><span class="sxs-lookup"><span data-stu-id="66796-183">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="66796-184">Są one częścią charakteru aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="66796-184">They are part of the nature of SPAs.</span></span> <span data-ttu-id="66796-185">SPA używający plików cookie również nie może wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="66796-185">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="66796-186">Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:</span><span class="sxs-lookup"><span data-stu-id="66796-186">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="66796-187">Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="66796-187">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="66796-188">Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="66796-188">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="66796-189">Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:</span><span class="sxs-lookup"><span data-stu-id="66796-189">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="66796-190">Żądanie powiodło się, a aplikacja ma prawidłowy token.</span><span class="sxs-lookup"><span data-stu-id="66796-190">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="66796-191">Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="66796-191">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="66796-192">Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania.</span><span class="sxs-lookup"><span data-stu-id="66796-192">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="66796-193">Istnieją różne podejścia z rosnącymi poziomami złożoności:</span><span class="sxs-lookup"><span data-stu-id="66796-193">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="66796-194">Przechowuj bieżący stan strony w magazynie sesji.</span><span class="sxs-lookup"><span data-stu-id="66796-194">Store the current page state in session storage.</span></span> <span data-ttu-id="66796-195">Przed kontynuowaniem sprawdź, czy w ramach [zdarzenia cyklu życia OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) można przywrócić stan.</span><span class="sxs-lookup"><span data-stu-id="66796-195">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="66796-196">Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.</span><span class="sxs-lookup"><span data-stu-id="66796-196">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="66796-197">Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.</span><span class="sxs-lookup"><span data-stu-id="66796-197">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="66796-198">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="66796-198">The following example shows how to:</span></span>

* <span data-ttu-id="66796-199">Zachowaj stan przed przekierowaniem do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="66796-199">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="66796-200">Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="66796-200">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="66796-201">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="66796-201">Save app state before an authentication operation</span></span>

<span data-ttu-id="66796-202">Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP.</span><span class="sxs-lookup"><span data-stu-id="66796-202">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="66796-203">Taka sytuacja może wystąpić w przypadku korzystania z takiego elementu jak kontenera stanu i przywrócenia stanu po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="66796-203">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="66796-204">Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="66796-204">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="66796-205">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="66796-205">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="66796-206">Dostosowywanie tras aplikacji</span><span class="sxs-lookup"><span data-stu-id="66796-206">Customize app routes</span></span>

<span data-ttu-id="66796-207">Domyślnie Biblioteka [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="66796-207">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="66796-208">Trasa</span><span class="sxs-lookup"><span data-stu-id="66796-208">Route</span></span>                            | <span data-ttu-id="66796-209">Przeznaczenie</span><span class="sxs-lookup"><span data-stu-id="66796-209">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="66796-210">Wyzwala operację logowania.</span><span class="sxs-lookup"><span data-stu-id="66796-210">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="66796-211">Obsługuje wynik operacji logowania.</span><span class="sxs-lookup"><span data-stu-id="66796-211">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="66796-212">Wyświetla komunikaty o błędach, gdy operacja logowania zakończy się niepowodzeniem z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="66796-212">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="66796-213">Wyzwala operację wylogowania.</span><span class="sxs-lookup"><span data-stu-id="66796-213">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="66796-214">Obsługuje wynik operacji wylogowania.</span><span class="sxs-lookup"><span data-stu-id="66796-214">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="66796-215">Wyświetla komunikaty o błędach, gdy operacja wylogowania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="66796-215">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="66796-216">Wskazuje, że użytkownik pomyślnie wylogować się.</span><span class="sxs-lookup"><span data-stu-id="66796-216">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="66796-217">Wyzwala operację edytowania profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="66796-217">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="66796-218">Wyzwala operację w celu zarejestrowania nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="66796-218">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="66796-219">Trasy przedstawione w powyższej tabeli można konfigurować za pośrednictwem programu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="66796-219">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="66796-220">Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="66796-220">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="66796-221">W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security` .</span><span class="sxs-lookup"><span data-stu-id="66796-221">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="66796-222">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="66796-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="66796-223">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="66796-223">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="66796-224">Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> parametr z jawnym akcją:</span><span class="sxs-lookup"><span data-stu-id="66796-224">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="66796-225">Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="66796-225">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="66796-226">Dostosowywanie interfejsu użytkownika uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="66796-226">Customize the authentication user interface</span></span>

<span data-ttu-id="66796-227"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="66796-227"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="66796-228">Każdy stan można dostosować, przekazując element niestandardowe <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="66796-228">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="66796-229">Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="66796-229">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="66796-230">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="66796-230">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="66796-231"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="66796-231">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="66796-232">Trasa</span><span class="sxs-lookup"><span data-stu-id="66796-232">Route</span></span>                            | <span data-ttu-id="66796-233">Fragment</span><span class="sxs-lookup"><span data-stu-id="66796-233">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="66796-234">Dostosowywanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="66796-234">Customize the user</span></span>

<span data-ttu-id="66796-235">Użytkownicy powiązana z aplikacją mogą być dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="66796-235">Users bound to the app can be customized.</span></span> <span data-ttu-id="66796-236">W poniższym przykładzie Wszyscy uwierzytelnieni użytkownicy otrzymują `amr` każde z metod uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="66796-236">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="66796-237">Utwórz klasę rozszerzającą <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> klasę:</span><span class="sxs-lookup"><span data-stu-id="66796-237">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="66796-238">Utwórz fabrykę, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="66796-238">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="66796-239">Zarejestruj `CustomAccountFactory` dostawcę uwierzytelniania w użyciu.</span><span class="sxs-lookup"><span data-stu-id="66796-239">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="66796-240">Wszystkie następujące rejestracje są prawidłowe:</span><span class="sxs-lookup"><span data-stu-id="66796-240">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="66796-241"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="66796-241"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="66796-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="66796-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="66796-243"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="66796-243"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="66796-244">Obsługa prerenderowania przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="66796-244">Support prerendering with authentication</span></span>

<span data-ttu-id="66796-245">Po zastosowaniu wskazówek w jednej z aplikacji hostowanego Blazor zestawu webassembly Skorzystaj z poniższych instrukcji, aby utworzyć aplikację, która:</span><span class="sxs-lookup"><span data-stu-id="66796-245">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="66796-246">Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="66796-246">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="66796-247">Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="66796-247">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="66796-248">W klasie aplikacji klienckiej `Program` (*program.cs*) należy wykonać typowe rejestracje usługi w oddzielnym metodzie (na przykład `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="66796-248">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="66796-249">W aplikacji serwera `Startup.ConfigureServices` zarejestruj następujące dodatkowe usługi:</span><span class="sxs-lookup"><span data-stu-id="66796-249">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="66796-250">W metodzie aplikacji serwera `Startup.Configure` Zastąp [punkty końcowe. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) z [punktami końcowymi. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="66796-250">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="66796-251">W aplikacji serwer Utwórz folder *strony* , jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="66796-251">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="66796-252">Utwórz stronę *_Host. cshtml* w folderze *strony* aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="66796-252">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="66796-253">Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="66796-253">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="66796-254">Aktualizuj zawartość pliku:</span><span class="sxs-lookup"><span data-stu-id="66796-254">Update the file's contents:</span></span>

* <span data-ttu-id="66796-255">Dodaj `@page "_Host"` na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="66796-255">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="66796-256">Zastąp `<app>Loading...</app>` tag następującym:</span><span class="sxs-lookup"><span data-stu-id="66796-256">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="66796-257">Opcje aplikacji hostowanych i dostawców logowania innych firm</span><span class="sxs-lookup"><span data-stu-id="66796-257">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="66796-258">Podczas uwierzytelniania i autoryzowania hostowanej Blazor aplikacji sieci webassembly przy użyciu dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="66796-258">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="66796-259">Wybór jednego z nich zależy od danego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="66796-259">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="66796-260">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="66796-260">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="66796-261">Uwierzytelnianie użytkowników tylko w celu wywołania chronionych interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="66796-261">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="66796-262">Uwierzytelnij użytkownika za pomocą przepływu OAuth po stronie klienta dla dostawcy interfejsu API innej firmy:</span><span class="sxs-lookup"><span data-stu-id="66796-262">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="66796-263">W tym scenariuszu:</span><span class="sxs-lookup"><span data-stu-id="66796-263">In this scenario:</span></span>

* <span data-ttu-id="66796-264">Serwer hostujący aplikację nie odgrywa roli.</span><span class="sxs-lookup"><span data-stu-id="66796-264">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="66796-265">Nie można chronić interfejsów API na serwerze.</span><span class="sxs-lookup"><span data-stu-id="66796-265">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="66796-266">Aplikacja może wywoływać tylko chronione interfejsy API innych firm.</span><span class="sxs-lookup"><span data-stu-id="66796-266">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="66796-267">Uwierzytelnianie użytkowników za pomocą dostawcy innych firm i wywoływanie chronionych interfejsów API na serwerze hosta i stronie trzeciej</span><span class="sxs-lookup"><span data-stu-id="66796-267">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="66796-268">Skonfiguruj Identity przy użyciu innego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="66796-268">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="66796-269">Uzyskaj tokeny wymagane przez dostęp do interfejsu API innych firm i Zapisz je.</span><span class="sxs-lookup"><span data-stu-id="66796-269">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="66796-270">Gdy użytkownik loguje się, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="66796-270">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="66796-271">W tym momencie istnieje kilka podejścia dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.</span><span class="sxs-lookup"><span data-stu-id="66796-271">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="66796-272">Korzystanie z tokenu dostępu do serwera w celu pobrania tokenu dostępu innej firmy</span><span class="sxs-lookup"><span data-stu-id="66796-272">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="66796-273">Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="66796-273">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="66796-274">Z tego miejsca Użyj tokenu dostępu innej firmy do wywołania zasobów interfejsu API innych firm bezpośrednio z poziomu Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="66796-274">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="66796-275">Nie zalecamy tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="66796-275">We don't recommend this approach.</span></span> <span data-ttu-id="66796-276">Takie podejście wymaga traktowania tokenu dostępu innej firmy, tak jakby został wygenerowany dla klienta publicznego.</span><span class="sxs-lookup"><span data-stu-id="66796-276">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="66796-277">W przypadku postanowień uwierzytelniania OAuth publiczna aplikacja nie ma tajnego klienta, ponieważ nie może być zaufana do bezpiecznego przechowywania wpisów tajnych, a token dostępu jest generowany dla klienta poufnego.</span><span class="sxs-lookup"><span data-stu-id="66796-277">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="66796-278">Klient poufny jest klientem, który ma klucz tajny klienta i ma możliwość bezpiecznego przechowywania wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="66796-278">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="66796-279">Token dostępu innej firmy może mieć przyznane dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że firma zewnętrzna emituje token dla bardziej zaufanego klienta.</span><span class="sxs-lookup"><span data-stu-id="66796-279">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="66796-280">Podobnie tokeny odświeżania nie powinny być wystawiane dla klienta, który nie jest zaufany. w takim przypadku klient nie będzie miał nieograniczonego dostępu, chyba że zostaną zastosowane inne ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="66796-280">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="66796-281">Wykonywanie wywołań interfejsu API z klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="66796-281">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="66796-282">Wykonaj wywołanie interfejsu API z klienta do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="66796-282">Make an API call from the client to the server API.</span></span> <span data-ttu-id="66796-283">Na serwerze programu Pobierz token dostępu dla zasobu interfejsu API innej firmy i wystaw, w jaki sposób jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="66796-283">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="66796-284">Chociaż to podejście wymaga dodatkowego skoku sieci przez serwer w celu wywołania interfejsu API innej firmy, ostatecznie powoduje bezpieczniejsze środowisko:</span><span class="sxs-lookup"><span data-stu-id="66796-284">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="66796-285">Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.</span><span class="sxs-lookup"><span data-stu-id="66796-285">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="66796-286">Aplikacja nie może wyciekować tokenów dostępu z serwera, który może zawierać bardziej poufne uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="66796-286">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="66796-287">Korzystanie z punktów końcowych programu Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="66796-287">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="66796-288">Biblioteka i szablony uwierzytelniania Blazor korzystają z punktów końcowych Open ID Connect (OIDC) w wersji 1.0.</span><span class="sxs-lookup"><span data-stu-id="66796-288">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="66796-289">Aby użyć punktu końcowego v 2.0, skonfiguruj opcję okaziciela JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="66796-289">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="66796-290">W poniższym przykładzie skonfigurowano usługi AAD dla programu v 2.0 przez dołączenie `v2.0` segmentu do <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="66796-290">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="66796-291">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="66796-291">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="66796-292">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="66796-292">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="66796-293">Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> lub w pliku ustawień aplikacji (*appSettings. JSON*) przy użyciu `Authority` klucza.</span><span class="sxs-lookup"><span data-stu-id="66796-293">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="66796-294">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="66796-294">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="66796-295">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="66796-295">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
