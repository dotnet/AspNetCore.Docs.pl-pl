---
title: ASP.NET Core Blazor WebAssembly dodatkowe scenariusze zabezpieczeń
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor WebAssembly dodatkowe scenariusze zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 8cb09b9ecf8fc67652125e92aa15b484526c121e
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504661"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="94095-103">ASP.NET Core Blazor WebAssembly dodatkowe scenariusze zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="94095-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="94095-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="94095-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="94095-105">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="94095-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="94095-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> jest <xref:System.Net.Http.DelegatingHandler> używany do dołączania tokenów dostępu do <xref:System.Net.Http.HttpResponseMessage> wystąpień wychodzących.</span><span class="sxs-lookup"><span data-stu-id="94095-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="94095-107">Tokeny są uzyskiwane przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> usługi, która jest rejestrowana przez platformę.</span><span class="sxs-lookup"><span data-stu-id="94095-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="94095-108">Jeśli nie można uzyskać tokenu, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="94095-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="94095-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="94095-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="94095-110">Dla wygody, struktura zapewnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> wstępnie skonfigurowany adres podstawowy aplikacji jako autoryzowany adres URL.</span><span class="sxs-lookup"><span data-stu-id="94095-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="94095-111">**Tokeny dostępu są dodawane tylko wtedy, gdy identyfikator URI żądania jest w podstawowym identyfikatorze URI aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="94095-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="94095-112">Gdy identyfikatory URI żądania wychodzącego nie znajdują się w podstawowym identyfikatorze URI aplikacji, użyj [niestandardowej `AuthorizationMessageHandler` klasy (*zalecane*)](#custom-authorizationmessagehandler-class) lub [Skonfiguruj `AuthorizationMessageHandler` ](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="94095-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="94095-113">Oprócz konfiguracji aplikacji klienta dostępu do interfejsu API serwera, interfejs API serwera musi również zezwalać na żądania między źródłami (CORS), gdy klient i serwer nie znajdują się na tym samym adresie podstawowym.</span><span class="sxs-lookup"><span data-stu-id="94095-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="94095-114">Aby uzyskać więcej informacji na temat konfiguracji funkcji CORS po stronie serwera, zobacz sekcję [udostępnianie zasobów między źródłami (CORS)](#cross-origin-resource-sharing-cors) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="94095-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="94095-115">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="94095-115">In the following example:</span></span>

* <span data-ttu-id="94095-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> dodaje <xref:System.Net.Http.IHttpClientFactory> i pokrewnych usług do kolekcji usług i konfiguruje nazwę <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="94095-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="94095-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> jest adresem podstawowym identyfikatora URI zasobu podczas wysyłania żądań.</span><span class="sxs-lookup"><span data-stu-id="94095-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="94095-118"><xref:System.Net.Http.IHttpClientFactory> jest dostarczany przez [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="94095-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="94095-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> jest <xref:System.Net.Http.DelegatingHandler> używany do dołączania tokenów dostępu do <xref:System.Net.Http.HttpResponseMessage> wystąpień wychodzących.</span><span class="sxs-lookup"><span data-stu-id="94095-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="94095-120">Tokeny dostępu są dodawane tylko wtedy, gdy identyfikator URI żądania jest w podstawowym identyfikatorze URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="94095-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="94095-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> tworzy i konfiguruje <xref:System.Net.Http.HttpClient> wystąpienie dla żądań wychodzących przy użyciu konfiguracji, która odnosi się do nazwy <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="94095-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="94095-122">W przypadku Blazor aplikacji na podstawie Blazor WebAssembly szablonu hostowanego projektu identyfikatory URI żądania są domyślnie w podstawowym identyfikatorze URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="94095-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="94095-123">W związku z tym <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> elementu w aplikacji wygenerowanej na podstawie szablonu projektu.</span><span class="sxs-lookup"><span data-stu-id="94095-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="94095-124">Skonfigurowana konfiguracja służy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) wzorca:</span><span class="sxs-lookup"><span data-stu-id="94095-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="94095-125">Klasa niestandardowa `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="94095-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="94095-126">*Te wskazówki w tej sekcji są zalecane dla aplikacji klienckich, które wysyłają żądania wychodzące do identyfikatorów URI, które nie znajdują się w podstawowym identyfikatorze URI aplikacji.*</span><span class="sxs-lookup"><span data-stu-id="94095-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="94095-127">W poniższym przykładzie Klasa niestandardowa rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> się do użycia jako <xref:System.Net.Http.DelegatingHandler> dla elementu <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="94095-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="94095-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguruje ten program obsługi do autoryzacji wychodzących żądań HTTP przy użyciu tokenu dostępu.</span><span class="sxs-lookup"><span data-stu-id="94095-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="94095-129">Token dostępu jest dołączany tylko wtedy, gdy co najmniej jeden z autoryzowanych adresów URL jest podstawą identyfikatora URI żądania ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="94095-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="94095-130">W programie `Program.Main` ( `Program.cs` ) program `CustomAuthorizationMessageHandler` jest zarejestrowany jako usługa o określonym zakresie i jest skonfigurowany jako <xref:System.Net.Http.DelegatingHandler> dla wystąpień wychodzących <xref:System.Net.Http.HttpResponseMessage> wykonanych przez nazwę <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="94095-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="94095-131">W przypadku Blazor aplikacji opartej na Blazor WebAssembly szablonie projektu hostowanego <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest domyślnie przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="94095-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="94095-132">Skonfigurowany <xref:System.Net.Http.HttpClient> jest używany do autoryzacji żądań za pomocą [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) wzorca.</span><span class="sxs-lookup"><span data-stu-id="94095-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="94095-133">Gdy klient jest tworzony przy użyciu <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakietu (pakiet), <xref:System.Net.Http.HttpClient> jest podano wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="94095-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="94095-134">Jeśli identyfikator URI żądania jest względnym identyfikatorem URI, tak jak w poniższym przykładzie ( `ExampleAPIMethod` ), jest on połączony z, <xref:System.Net.Http.HttpClient.BaseAddress> gdy aplikacja kliencka wysyła żądanie:</span><span class="sxs-lookup"><span data-stu-id="94095-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="94095-135">Ponowne `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="94095-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="94095-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="94095-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="94095-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguruje procedurę obsługi autoryzacji wychodzących żądań HTTP przy użyciu tokenu dostępu.</span><span class="sxs-lookup"><span data-stu-id="94095-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="94095-138">Token dostępu jest dołączany tylko wtedy, gdy co najmniej jeden z autoryzowanych adresów URL jest podstawą identyfikatora URI żądania ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="94095-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="94095-139">Jeśli identyfikator URI żądania jest względnym identyfikatorem URI, jest on połączony z <xref:System.Net.Http.HttpClient.BaseAddress> .</span><span class="sxs-lookup"><span data-stu-id="94095-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="94095-140">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> konfiguruje <xref:System.Net.Http.HttpClient> w `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="94095-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="94095-141">W przypadku Blazor aplikacji na podstawie Blazor WebAssembly szablonu hostowanego projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> domyślnie przypisywany jest następujący element:</span><span class="sxs-lookup"><span data-stu-id="94095-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="94095-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="94095-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="94095-143">Adres URL `authorizedUrls` tablicy.</span><span class="sxs-lookup"><span data-stu-id="94095-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="94095-144">Wpisane `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="94095-144">Typed `HttpClient`</span></span>

<span data-ttu-id="94095-145">Można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy.</span><span class="sxs-lookup"><span data-stu-id="94095-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="94095-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="94095-146">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="94095-147">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="94095-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="94095-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="94095-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="94095-149">W przypadku Blazor aplikacji opartej na Blazor WebAssembly szablonie projektu hostowanego <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest domyślnie przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="94095-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="94095-150">`FetchData` składnik ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="94095-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="94095-151">Skonfiguruj `HttpClient` procedurę obsługi</span><span class="sxs-lookup"><span data-stu-id="94095-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="94095-152">Program obsługi można skonfigurować w taki sposób, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Aby wychodzące żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="94095-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="94095-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="94095-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="94095-154">W przypadku Blazor aplikacji na podstawie Blazor WebAssembly szablonu hostowanego projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> domyślnie przypisywany jest następujący element:</span><span class="sxs-lookup"><span data-stu-id="94095-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="94095-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="94095-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="94095-156">Adres URL `authorizedUrls` tablicy.</span><span class="sxs-lookup"><span data-stu-id="94095-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="94095-157">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="94095-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="94095-158">Jeśli Blazor WebAssembly aplikacja zwykle korzysta z bezpiecznego ustawienia domyślnego <xref:System.Net.Http.HttpClient> , aplikacja może również wykonać nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web przez skonfigurowanie nazwanego elementu <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="94095-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="94095-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="94095-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="94095-160">W przypadku Blazor aplikacji opartej na Blazor WebAssembly szablonie projektu hostowanego <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest domyślnie przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="94095-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="94095-161">Poprzednia rejestracja jest uzupełnieniem istniejącej bezpiecznej rejestracji domyślnej <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="94095-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="94095-162">Składnik tworzy <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakiet), aby wykonać nieuwierzytelnione lub nieautoryzowane żądania:</span><span class="sxs-lookup"><span data-stu-id="94095-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="94095-163">Kontroler w interfejsie API serwera, `WeatherForecastNoAuthenticationController` dla poprzedniego przykładu, nie jest oznaczony [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="94095-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="94095-164">Decyzja o konieczności użycia bezpiecznego klienta lub niezabezpieczonego klienta jako <xref:System.Net.Http.HttpClient> wystąpienia domyślnego do dewelopera.</span><span class="sxs-lookup"><span data-stu-id="94095-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="94095-165">Jednym ze sposobów podjęcia tej decyzji jest uwzględnienie liczby uwierzytelnionych i nieuwierzytelnionych punktów końcowych, z którymi kontaktuje się aplikacja.</span><span class="sxs-lookup"><span data-stu-id="94095-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="94095-166">Jeśli większość żądań aplikacji jest zabezpieczonych punktów końcowych interfejsu API, użyj uwierzytelnionego <xref:System.Net.Http.HttpClient> wystąpienia jako domyślnego.</span><span class="sxs-lookup"><span data-stu-id="94095-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="94095-167">W przeciwnym razie Zarejestruj nieuwierzytelnione <xref:System.Net.Http.HttpClient> wystąpienie jako domyślne.</span><span class="sxs-lookup"><span data-stu-id="94095-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="94095-168">Alternatywnym podejściem do korzystania z programu <xref:System.Net.Http.IHttpClientFactory> jest utworzenie [klienta o określonym typie](#typed-httpclient) dla nieuwierzytelnionego dostępu do anonimowych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="94095-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="94095-169">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="94095-169">Request additional access tokens</span></span>

<span data-ttu-id="94095-170">Tokeny dostępu można uzyskać ręcznie, wywołując metodę `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="94095-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="94095-171">W poniższym przykładzie dodatkowe zakresy interfejsu API usługi Azure Active Directory (AAD) Microsoft Graph są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="94095-171">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="94095-172">Po dodaniu Microsoft Graph uprawnień interfejsu API w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="94095-172">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="94095-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="94095-173">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="94095-174">`IAccessTokenProvider.RequestToken`Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów.</span><span class="sxs-lookup"><span data-stu-id="94095-174">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="94095-175">W Razor składniku:</span><span class="sxs-lookup"><span data-stu-id="94095-175">In a Razor component:</span></span>

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

<span data-ttu-id="94095-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> typu</span><span class="sxs-lookup"><span data-stu-id="94095-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="94095-177">`true` z `token` do użycia.</span><span class="sxs-lookup"><span data-stu-id="94095-177">`true` with the `token` for use.</span></span>
* <span data-ttu-id="94095-178">`false` Jeśli nie pobrano tokenu.</span><span class="sxs-lookup"><span data-stu-id="94095-178">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="94095-179">`HttpClient` i `HttpRequestMessage` z opcjami żądania interfejsu API pobierania</span><span class="sxs-lookup"><span data-stu-id="94095-179">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="94095-180">W przypadku uruchamiania w zestawie webassembly w Blazor WebAssembly aplikacji [`HttpClient`](xref:fundamentals/http-requests) ([Dokumentacja interfejsu API](xref:System.Net.Http.HttpClient)) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań.</span><span class="sxs-lookup"><span data-stu-id="94095-180">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="94095-181">Na przykład można określić metodę HTTP i nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="94095-181">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="94095-182">Poniższy składnik wysyła `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="94095-182">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@responseBody</p>

@code {
    private string responseBody;

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

            responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="94095-183">Implementacja programu .NET webassembly <xref:System.Net.Http.HttpClient> używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="94095-183">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="94095-184">Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="94095-184">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="94095-185">Opcje żądania pobrania HTTP można skonfigurować przy użyciu <xref:System.Net.Http.HttpRequestMessage> metod rozszerzających pokazanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="94095-185">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="94095-186">Metoda rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="94095-186">Extension method</span></span> | <span data-ttu-id="94095-187">Właściwość żądania pobrania</span><span class="sxs-lookup"><span data-stu-id="94095-187">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="94095-188">Można ustawić dodatkowe opcje przy użyciu bardziej generycznej <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="94095-188">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="94095-189">Odpowiedź HTTP jest zwykle buforowana w Blazor WebAssembly aplikacji, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="94095-189">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="94095-190">Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy użyć <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metody rozszerzenia w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="94095-190">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="94095-191">Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="94095-191">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="94095-192">Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="94095-192">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="94095-193">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="94095-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="94095-194">W przypadku wysyłania poświadczeń ( cookie nagłówki autoryzacji/nagłówków) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="94095-194">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="94095-195">Następujące zasady obejmują konfigurację programu:</span><span class="sxs-lookup"><span data-stu-id="94095-195">The following policy includes configuration for:</span></span>

* <span data-ttu-id="94095-196">Pochodzenie żądania ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="94095-196">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="94095-197">Dowolna Metoda (czasownik).</span><span class="sxs-lookup"><span data-stu-id="94095-197">Any method (verb).</span></span>
* <span data-ttu-id="94095-198">`Content-Type` i `Authorization` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="94095-198">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="94095-199">Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header` ), Wyświetl nagłówek podczas wywoływania <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="94095-199">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="94095-200">Poświadczenia ustawione przez kod JavaScript po stronie klienta ( `credentials` Właściwość ustawiona na `include` ).</span><span class="sxs-lookup"><span data-stu-id="94095-200">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="94095-201">Rozwiązanie hostowane Blazor oparte na Blazor szablonie projektu hostowanego używa tego samego adresu podstawowego dla aplikacji klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="94095-201">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="94095-202">Aplikacja kliencka <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> jest domyślnie ustawiona na identyfikator URI `builder.HostEnvironment.BaseAddress` .</span><span class="sxs-lookup"><span data-stu-id="94095-202">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="94095-203">Konfiguracja mechanizmu CORS **nie** jest wymagana w domyślnej konfiguracji hostowanej aplikacji utworzonej na podstawie Blazor szablonu hostowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="94095-203">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="94095-204">Dodatkowe aplikacje klienckie, które nie są hostowane przez projekt serwera **i nie współdzielą adresu podstawowego** aplikacji serwera, wymagają konfiguracji CORS w projekcie serwera.</span><span class="sxs-lookup"><span data-stu-id="94095-204">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="94095-205">Aby uzyskać więcej informacji, zobacz <xref:security/cors> i składnik Tester żądania HTTP aplikacji przykładowej ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="94095-205">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="94095-206">Obsługa błędów żądania tokenu</span><span class="sxs-lookup"><span data-stu-id="94095-206">Handle token request errors</span></span>

<span data-ttu-id="94095-207">Gdy aplikacja jednostronicowa uwierzytelnia użytkownika przy użyciu usługi OpenID Connect Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w Identity postaci sesji cookie , która jest ustawiona jako wynik użytkownika dostarczającego poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="94095-207">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="94095-208">Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny.</span><span class="sxs-lookup"><span data-stu-id="94095-208">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="94095-209">W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów.</span><span class="sxs-lookup"><span data-stu-id="94095-209">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="94095-210">W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.</span><span class="sxs-lookup"><span data-stu-id="94095-210">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="94095-211">Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP.</span><span class="sxs-lookup"><span data-stu-id="94095-211">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="94095-212">Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="94095-212">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="94095-213">Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.</span><span class="sxs-lookup"><span data-stu-id="94095-213">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="94095-214">Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach.</span><span class="sxs-lookup"><span data-stu-id="94095-214">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="94095-215">Są one częścią charakteru aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="94095-215">They are part of the nature of SPAs.</span></span> <span data-ttu-id="94095-216">SPA używa cookie również protokołu API serwera, jeśli uwierzytelnianie cookie zostało usunięte.</span><span class="sxs-lookup"><span data-stu-id="94095-216">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="94095-217">Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:</span><span class="sxs-lookup"><span data-stu-id="94095-217">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="94095-218">Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="94095-218">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="94095-219">Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="94095-219">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="94095-220">Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:</span><span class="sxs-lookup"><span data-stu-id="94095-220">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="94095-221">Żądanie powiodło się, a aplikacja ma prawidłowy token.</span><span class="sxs-lookup"><span data-stu-id="94095-221">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="94095-222">Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="94095-222">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="94095-223">Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania.</span><span class="sxs-lookup"><span data-stu-id="94095-223">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="94095-224">Istnieją różne podejścia z rosnącymi poziomami złożoności:</span><span class="sxs-lookup"><span data-stu-id="94095-224">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="94095-225">Przechowuj bieżący stan strony w magazynie sesji.</span><span class="sxs-lookup"><span data-stu-id="94095-225">Store the current page state in session storage.</span></span> <span data-ttu-id="94095-226">Podczas [ `OnInitializedAsync` zdarzenia cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) Sprawdź, czy stan można przywrócić przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="94095-226">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="94095-227">Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.</span><span class="sxs-lookup"><span data-stu-id="94095-227">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="94095-228">Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.</span><span class="sxs-lookup"><span data-stu-id="94095-228">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="94095-229">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="94095-229">The following example shows how to:</span></span>

* <span data-ttu-id="94095-230">Zachowaj stan przed przekierowaniem do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="94095-230">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="94095-231">Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="94095-231">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="94095-232">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="94095-232">Save app state before an authentication operation</span></span>

<span data-ttu-id="94095-233">Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP.</span><span class="sxs-lookup"><span data-stu-id="94095-233">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="94095-234">Może to być przypadek, gdy używasz kontenera stanu i chcesz przywrócić stan po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="94095-234">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="94095-235">Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="94095-235">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="94095-236">Poniższy przykład demonstruje podejście.</span><span class="sxs-lookup"><span data-stu-id="94095-236">The following example demonstrates the approach.</span></span>

<span data-ttu-id="94095-237">W aplikacji jest tworzona Klasa kontenera stanu z właściwościami do przechowywania wartości stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="94095-237">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="94095-238">W poniższym przykładzie kontener jest używany do utrzymania wartości licznika domyślnego składnika szablonu projektu `Counter` ( `Pages/Counter.razor` ).</span><span class="sxs-lookup"><span data-stu-id="94095-238">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="94095-239">Metody serializacji i deserializacji kontenera są oparte na <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="94095-239">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="94095-240">`Counter`Składnik używa kontenera stanu, aby zachować `currentCount` wartość spoza składnika:</span><span class="sxs-lookup"><span data-stu-id="94095-240">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="94095-241">Utwórz element `ApplicationAuthenticationState` z <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="94095-241">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="94095-242">Podaj `Id` Właściwość, która służy jako identyfikator dla stanu przechowywanego lokalnie.</span><span class="sxs-lookup"><span data-stu-id="94095-242">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="94095-243">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="94095-243">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="94095-244">`Authentication`Składnik ( `Pages/Authentication.razor` ) zapisuje i przywraca stan aplikacji za pomocą lokalnego magazynu sesji przy użyciu `StateContainer` metod serializacji i deserializacji `GetStateForLocalStorage` oraz `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="94095-244">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="94095-245">Ten przykład używa Azure Active Directory (AAD) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="94095-245">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="94095-246">W `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="94095-246">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="94095-247">`ApplicationAuthenticationState`Jest skonfigurowany jako typ biblioteki Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="94095-247">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="94095-248">Kontener stanu jest zarejestrowany w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="94095-248">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="94095-249">Dostosowywanie tras aplikacji</span><span class="sxs-lookup"><span data-stu-id="94095-249">Customize app routes</span></span>

<span data-ttu-id="94095-250">Domyślnie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) Biblioteka używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="94095-250">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="94095-251">Trasa</span><span class="sxs-lookup"><span data-stu-id="94095-251">Route</span></span>                            | <span data-ttu-id="94095-252">Przeznaczenie</span><span class="sxs-lookup"><span data-stu-id="94095-252">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="94095-253">Wyzwala operację logowania.</span><span class="sxs-lookup"><span data-stu-id="94095-253">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="94095-254">Obsługuje wynik operacji logowania.</span><span class="sxs-lookup"><span data-stu-id="94095-254">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="94095-255">Wyświetla komunikaty o błędach, gdy operacja logowania zakończy się niepowodzeniem z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="94095-255">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="94095-256">Wyzwala operację wylogowania.</span><span class="sxs-lookup"><span data-stu-id="94095-256">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="94095-257">Obsługuje wynik operacji wylogowania.</span><span class="sxs-lookup"><span data-stu-id="94095-257">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="94095-258">Wyświetla komunikaty o błędach, gdy operacja wylogowania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="94095-258">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="94095-259">Wskazuje, że użytkownik pomyślnie wylogować się.</span><span class="sxs-lookup"><span data-stu-id="94095-259">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="94095-260">Wyzwala operację edytowania profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="94095-260">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="94095-261">Wyzwala operację w celu zarejestrowania nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="94095-261">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="94095-262">Trasy przedstawione w powyższej tabeli można konfigurować za pośrednictwem programu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="94095-262">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="94095-263">Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="94095-263">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="94095-264">W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security` .</span><span class="sxs-lookup"><span data-stu-id="94095-264">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="94095-265">`Authentication` składnik ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="94095-265">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="94095-266">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="94095-266">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="94095-267">Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> parametr z jawnym akcją:</span><span class="sxs-lookup"><span data-stu-id="94095-267">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="94095-268">Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="94095-268">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="94095-269">Dostosowywanie interfejsu użytkownika uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="94095-269">Customize the authentication user interface</span></span>

<span data-ttu-id="94095-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="94095-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="94095-271">Każdy stan można dostosować, przekazując element niestandardowe <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="94095-271">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="94095-272">Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="94095-272">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="94095-273">`Authentication` składnik ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="94095-273">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="94095-274"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="94095-274">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="94095-275">Trasa</span><span class="sxs-lookup"><span data-stu-id="94095-275">Route</span></span>                            | <span data-ttu-id="94095-276">Fragment</span><span class="sxs-lookup"><span data-stu-id="94095-276">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="94095-277">Dostosowywanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="94095-277">Customize the user</span></span>

<span data-ttu-id="94095-278">Użytkownicy powiązana z aplikacją mogą być dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="94095-278">Users bound to the app can be customized.</span></span> <span data-ttu-id="94095-279">W poniższym przykładzie Wszyscy uwierzytelnieni użytkownicy otrzymują `amr` każde z metod uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="94095-279">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="94095-280">Utwórz klasę rozszerzającą <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> klasę:</span><span class="sxs-lookup"><span data-stu-id="94095-280">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="94095-281">Utwórz fabrykę, która rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="94095-281">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="94095-282">Zarejestruj `CustomAccountFactory` dostawcę uwierzytelniania w użyciu.</span><span class="sxs-lookup"><span data-stu-id="94095-282">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="94095-283">Wszystkie następujące rejestracje są prawidłowe:</span><span class="sxs-lookup"><span data-stu-id="94095-283">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="94095-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="94095-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="94095-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="94095-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="94095-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="94095-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="94095-287">Obsługa prerenderowania przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="94095-287">Support prerendering with authentication</span></span>

<span data-ttu-id="94095-288">Po zastosowaniu wskazówek w jednej z Blazor WebAssembly tematów aplikacji hostowanych należy wykonać następujące instrukcje, aby utworzyć aplikację, która:</span><span class="sxs-lookup"><span data-stu-id="94095-288">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="94095-289">Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="94095-289">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="94095-290">Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="94095-290">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="94095-291">W klasie aplikacji klienta `Program` ( `Program.cs` ) należy wykonać typowe rejestracje usługi w oddzielnym metodzie (na przykład `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="94095-291">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
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

<span data-ttu-id="94095-292">W aplikacji serwera `Startup.ConfigureServices` zarejestruj następujące dodatkowe usługi:</span><span class="sxs-lookup"><span data-stu-id="94095-292">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="94095-293">W metodzie aplikacji serwera `Startup.Configure` Zastąp ciąg [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) opcją [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="94095-293">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="94095-294">W aplikacji serwer Utwórz `Pages` folder, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="94095-294">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="94095-295">Utwórz `_Host.cshtml` stronę w folderze aplikacji serwera `Pages` .</span><span class="sxs-lookup"><span data-stu-id="94095-295">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="94095-296">Wklej zawartość z pliku aplikacji klienckiej `wwwroot/index.html` do `Pages/_Host.cshtml` pliku.</span><span class="sxs-lookup"><span data-stu-id="94095-296">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="94095-297">Aktualizuj zawartość pliku:</span><span class="sxs-lookup"><span data-stu-id="94095-297">Update the file's contents:</span></span>

* <span data-ttu-id="94095-298">Dodaj `@page "_Host"` na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="94095-298">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="94095-299">Zastąp `<app>Loading...</app>` tag następującym:</span><span class="sxs-lookup"><span data-stu-id="94095-299">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="94095-300">Opcje aplikacji hostowanych i dostawców logowania innych firm</span><span class="sxs-lookup"><span data-stu-id="94095-300">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="94095-301">Podczas uwierzytelniania i autoryzowania hostowanej Blazor WebAssembly aplikacji za pomocą dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="94095-301">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="94095-302">Wybór jednego z nich zależy od danego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="94095-302">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="94095-303">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="94095-303">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="94095-304">Uwierzytelnianie użytkowników tylko w celu wywołania chronionych interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="94095-304">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="94095-305">Uwierzytelnij użytkownika za pomocą przepływu OAuth po stronie klienta dla dostawcy interfejsu API innej firmy:</span><span class="sxs-lookup"><span data-stu-id="94095-305">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="94095-306">W tym scenariuszu:</span><span class="sxs-lookup"><span data-stu-id="94095-306">In this scenario:</span></span>

* <span data-ttu-id="94095-307">Serwer hostujący aplikację nie odgrywa roli.</span><span class="sxs-lookup"><span data-stu-id="94095-307">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="94095-308">Nie można chronić interfejsów API na serwerze.</span><span class="sxs-lookup"><span data-stu-id="94095-308">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="94095-309">Aplikacja może wywoływać tylko chronione interfejsy API innych firm.</span><span class="sxs-lookup"><span data-stu-id="94095-309">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="94095-310">Uwierzytelnianie użytkowników za pomocą dostawcy innych firm i wywoływanie chronionych interfejsów API na serwerze hosta i stronie trzeciej</span><span class="sxs-lookup"><span data-stu-id="94095-310">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="94095-311">Skonfiguruj Identity przy użyciu innego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="94095-311">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="94095-312">Uzyskaj tokeny wymagane przez dostęp do interfejsu API innych firm i Zapisz je.</span><span class="sxs-lookup"><span data-stu-id="94095-312">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="94095-313">Gdy użytkownik loguje się, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="94095-313">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="94095-314">W tym momencie istnieje kilka podejścia dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.</span><span class="sxs-lookup"><span data-stu-id="94095-314">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="94095-315">Korzystanie z tokenu dostępu do serwera w celu pobrania tokenu dostępu innej firmy</span><span class="sxs-lookup"><span data-stu-id="94095-315">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="94095-316">Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="94095-316">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="94095-317">Z tego miejsca Użyj tokenu dostępu innej firmy do wywołania zasobów interfejsu API innych firm bezpośrednio z poziomu Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="94095-317">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="94095-318">Nie zalecamy tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="94095-318">We don't recommend this approach.</span></span> <span data-ttu-id="94095-319">Takie podejście wymaga traktowania tokenu dostępu innej firmy, tak jakby został wygenerowany dla klienta publicznego.</span><span class="sxs-lookup"><span data-stu-id="94095-319">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="94095-320">W przypadku postanowień uwierzytelniania OAuth publiczna aplikacja nie ma tajnego klienta, ponieważ nie może być zaufana do bezpiecznego przechowywania wpisów tajnych, a token dostępu jest generowany dla klienta poufnego.</span><span class="sxs-lookup"><span data-stu-id="94095-320">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="94095-321">Klient poufny jest klientem, który ma klucz tajny klienta i ma możliwość bezpiecznego przechowywania wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="94095-321">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="94095-322">Token dostępu innej firmy może mieć przyznane dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że firma zewnętrzna emituje token dla bardziej zaufanego klienta.</span><span class="sxs-lookup"><span data-stu-id="94095-322">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="94095-323">Podobnie tokeny odświeżania nie powinny być wystawiane dla klienta, który nie jest zaufany. w takim przypadku klient nie będzie miał nieograniczonego dostępu, chyba że zostaną zastosowane inne ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="94095-323">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="94095-324">Wykonywanie wywołań interfejsu API z klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="94095-324">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="94095-325">Wykonaj wywołanie interfejsu API z klienta do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="94095-325">Make an API call from the client to the server API.</span></span> <span data-ttu-id="94095-326">Na serwerze programu Pobierz token dostępu dla zasobu interfejsu API innej firmy i wystaw, w jaki sposób jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="94095-326">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="94095-327">Chociaż to podejście wymaga dodatkowego skoku sieci przez serwer w celu wywołania interfejsu API innej firmy, ostatecznie powoduje bezpieczniejsze środowisko:</span><span class="sxs-lookup"><span data-stu-id="94095-327">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="94095-328">Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.</span><span class="sxs-lookup"><span data-stu-id="94095-328">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="94095-329">Aplikacja nie może wyciekować tokenów dostępu z serwera, który może zawierać bardziej poufne uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="94095-329">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="94095-330">Korzystanie z punktów końcowych OpenID Connect Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="94095-330">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="94095-331">Biblioteka uwierzytelniania i Blazor Szablony projektów korzystają z punktów końcowych OpenID Connect Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="94095-331">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="94095-332">Aby użyć punktu końcowego v 2.0, skonfiguruj opcję okaziciela JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="94095-332">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="94095-333">W poniższym przykładzie skonfigurowano usługi AAD dla programu v 2.0 przez dołączenie `v2.0` segmentu do <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Właściwości:</span><span class="sxs-lookup"><span data-stu-id="94095-333">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="94095-334">Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="94095-334">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="94095-335">Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="94095-335">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="94095-336">Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> lub w pliku ustawień aplikacji ( `appsettings.json` ) przy użyciu `Authority` klucza.</span><span class="sxs-lookup"><span data-stu-id="94095-336">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="94095-337">Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0.</span><span class="sxs-lookup"><span data-stu-id="94095-337">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="94095-338">Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="94095-338">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="94095-339">Konfigurowanie i używanie gRPC w składnikach</span><span class="sxs-lookup"><span data-stu-id="94095-339">Configure and use gRPC in components</span></span>

<span data-ttu-id="94095-340">Aby skonfigurować Blazor WebAssembly aplikację do używania [ASP.NET Core gRPC Framework](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="94095-340">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="94095-341">Włącz gRPC-Web na serwerze.</span><span class="sxs-lookup"><span data-stu-id="94095-341">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="94095-342">Aby uzyskać więcej informacji, zobacz <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="94095-342">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="94095-343">Zarejestruj usługi gRPC dla programu obsługi komunikatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="94095-343">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="94095-344">Poniższy przykład konfiguruje procedurę obsługi komunikatów autoryzacji aplikacji do korzystania [ `GreeterClient` z usługi z samouczka gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):</span><span class="sxs-lookup"><span data-stu-id="94095-344">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="94095-345">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="94095-345">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="94095-346">Umieść `.proto` plik w `Shared` projekcie rozwiązania hostowanego Blazor .</span><span class="sxs-lookup"><span data-stu-id="94095-346">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="94095-347">Składnik w aplikacji klienckiej może wykonywać wywołania gRPC przy użyciu klienta gRPC ( `Pages/Grpc.razor` ):</span><span class="sxs-lookup"><span data-stu-id="94095-347">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="94095-348">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="94095-348">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="94095-349">Aby użyć `Status.DebugException` właściwości, użyj [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) w wersji 2.30.0 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="94095-349">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="94095-350">Aby uzyskać więcej informacji, zobacz <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="94095-350">For more information, see <xref:grpc/browser>.</span></span>
