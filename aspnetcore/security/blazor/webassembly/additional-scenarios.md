---
title: Dodatkowe Blazor scenariusze zabezpieczeń ASP.NET Core webassembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: cd1433d5716b9b595270209fa874a8cb93fdf699
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138433"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="96e74-102">Dodatkowe scenariusze zabezpieczeń ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="96e74-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="96e74-103">Autor [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="96e74-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="96e74-104">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="96e74-104">Request additional access tokens</span></span>

<span data-ttu-id="96e74-105">Większość aplikacji wymaga tylko tokenu dostępu, aby móc korzystać z chronionych zasobów, z których korzystają.</span><span class="sxs-lookup"><span data-stu-id="96e74-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="96e74-106">W niektórych scenariuszach aplikacja może wymagać więcej niż jednego tokenu, aby można było korzystać z dwóch lub więcej zasobów.</span><span class="sxs-lookup"><span data-stu-id="96e74-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="96e74-107">W poniższym przykładzie dodatkowe zakresy interfejsu API usługi Azure Active Directory (AAD) Microsoft Graph są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="96e74-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="96e74-108">Po dodaniu Microsoft Graph uprawnień interfejsu API w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienckiej (`Program.Main`, *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96e74-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="96e74-109">`IAccessTokenProvider.RequestToken` Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="96e74-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
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

<span data-ttu-id="96e74-110">`TryGetToken`typu</span><span class="sxs-lookup"><span data-stu-id="96e74-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="96e74-111">`true`z `token` do użycia.</span><span class="sxs-lookup"><span data-stu-id="96e74-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="96e74-112">`false`Jeśli nie pobrano tokenu.</span><span class="sxs-lookup"><span data-stu-id="96e74-112">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="96e74-113">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="96e74-113">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="96e74-114">`AuthorizationMessageHandler` Usługa może być używana z `HttpClient` programem w celu dołączania tokenów dostępu do żądań wychodzących.</span><span class="sxs-lookup"><span data-stu-id="96e74-114">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="96e74-115">Tokeny są uzyskiwane przy `IAccessTokenProvider` użyciu istniejącej usługi.</span><span class="sxs-lookup"><span data-stu-id="96e74-115">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="96e74-116">Jeśli nie można uzyskać tokenu, zgłaszany `AccessTokenNotAvailableException` jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="96e74-116">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="96e74-117">`AccessTokenNotAvailableException`ma `Redirect` metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="96e74-117">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="96e74-118">`AuthorizationMessageHandler` Można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL `ConfigureHandler` przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="96e74-118">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="96e74-119">W poniższym `AuthorizationMessageHandler` przykładzie konfiguruje `HttpClient` w `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96e74-119">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
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

<span data-ttu-id="96e74-120">Dla wygody `BaseAddressAuthorizationMessageHandler` jest dołączony wstępnie skonfigurowany przy użyciu adresu podstawowego aplikacji jako autoryzowany adres URL.</span><span class="sxs-lookup"><span data-stu-id="96e74-120">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="96e74-121">Szablony zestawów webassembly z włączonym uwierzytelnianiem Blazor teraz używają [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) w celu `HttpClient` skonfigurowania `BaseAddressAuthorizationMessageHandler`programu przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="96e74-121">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="96e74-122">W przypadku, gdy klient został `CreateClient` utworzony za pomocą programu w powyższym przykładzie, `HttpClient` jest dostarczane wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="96e74-122">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="96e74-123">Skonfigurowany `HttpClient` jest następnie używany do autoryzowania żądań przy użyciu prostego `try-catch` wzorca.</span><span class="sxs-lookup"><span data-stu-id="96e74-123">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="96e74-124">Następujący `FetchData` składnik żąda danych prognozy pogody:</span><span class="sxs-lookup"><span data-stu-id="96e74-124">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="96e74-125">Alternatywnie można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy:</span><span class="sxs-lookup"><span data-stu-id="96e74-125">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="96e74-126">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e74-126">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="96e74-127">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e74-127">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="96e74-128">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="96e74-128">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="96e74-129">Obsługa błędów żądania tokenu</span><span class="sxs-lookup"><span data-stu-id="96e74-129">Handle token request errors</span></span>

<span data-ttu-id="96e74-130">Gdy aplikacja jednostronicowa uwierzytelnia użytkownika za pomocą funkcji Open ID Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w postaci pliku cookie sesji, który jest ustawiany w wyniku użytkownika dostarczającego poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="96e74-130">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="96e74-131">Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny.</span><span class="sxs-lookup"><span data-stu-id="96e74-131">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="96e74-132">W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów.</span><span class="sxs-lookup"><span data-stu-id="96e74-132">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="96e74-133">W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.</span><span class="sxs-lookup"><span data-stu-id="96e74-133">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="96e74-134">Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP.</span><span class="sxs-lookup"><span data-stu-id="96e74-134">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="96e74-135">Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="96e74-135">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="96e74-136">Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.</span><span class="sxs-lookup"><span data-stu-id="96e74-136">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="96e74-137">Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach.</span><span class="sxs-lookup"><span data-stu-id="96e74-137">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="96e74-138">Są one częścią charakteru aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="96e74-138">They are part of the nature of SPAs.</span></span> <span data-ttu-id="96e74-139">SPA używający plików cookie również nie może wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="96e74-139">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="96e74-140">Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:</span><span class="sxs-lookup"><span data-stu-id="96e74-140">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="96e74-141">Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="96e74-141">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="96e74-142">Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="96e74-142">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="96e74-143">Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:</span><span class="sxs-lookup"><span data-stu-id="96e74-143">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="96e74-144">Żądanie powiodło się, a aplikacja ma prawidłowy token.</span><span class="sxs-lookup"><span data-stu-id="96e74-144">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="96e74-145">Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.</span><span class="sxs-lookup"><span data-stu-id="96e74-145">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="96e74-146">Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-146">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="96e74-147">Istnieją różne podejścia z rosnącymi poziomami złożoności:</span><span class="sxs-lookup"><span data-stu-id="96e74-147">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="96e74-148">Przechowuj bieżący stan strony w magazynie sesji.</span><span class="sxs-lookup"><span data-stu-id="96e74-148">Store the current page state in session storage.</span></span> <span data-ttu-id="96e74-149">Przed `OnInitializeAsync`kontynuowaniem sprawdź, czy stan można przywrócić.</span><span class="sxs-lookup"><span data-stu-id="96e74-149">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="96e74-150">Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.</span><span class="sxs-lookup"><span data-stu-id="96e74-150">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="96e74-151">Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.</span><span class="sxs-lookup"><span data-stu-id="96e74-151">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="96e74-152">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="96e74-152">The following example shows how to:</span></span>

* <span data-ttu-id="96e74-153">Zachowaj stan przed przekierowaniem do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-153">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="96e74-154">Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="96e74-154">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="96e74-155">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="96e74-155">Save app state before an authentication operation</span></span>

<span data-ttu-id="96e74-156">Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP.</span><span class="sxs-lookup"><span data-stu-id="96e74-156">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="96e74-157">Taka sytuacja może wystąpić w przypadku korzystania z takiego elementu jak kontenera stanu i przywrócenia stanu po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="96e74-157">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="96e74-158">Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="96e74-158">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="96e74-159">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="96e74-159">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="96e74-160">Dostosowywanie tras aplikacji</span><span class="sxs-lookup"><span data-stu-id="96e74-160">Customize app routes</span></span>

<span data-ttu-id="96e74-161">Domyślnie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Biblioteka używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="96e74-161">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="96e74-162">Trasa</span><span class="sxs-lookup"><span data-stu-id="96e74-162">Route</span></span>                            | <span data-ttu-id="96e74-163">Przeznaczenie</span><span class="sxs-lookup"><span data-stu-id="96e74-163">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="96e74-164">Wyzwala operację logowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-164">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="96e74-165">Obsługuje wynik operacji logowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-165">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="96e74-166">Wyświetla komunikaty o błędach, gdy operacja logowania zakończy się niepowodzeniem z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="96e74-166">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="96e74-167">Wyzwala operację wylogowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-167">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="96e74-168">Obsługuje wynik operacji wylogowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-168">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="96e74-169">Wyświetla komunikaty o błędach, gdy operacja wylogowania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="96e74-169">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="96e74-170">Wskazuje, że użytkownik pomyślnie wylogować się.</span><span class="sxs-lookup"><span data-stu-id="96e74-170">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="96e74-171">Wyzwala operację edytowania profilu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="96e74-171">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="96e74-172">Wyzwala operację w celu zarejestrowania nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="96e74-172">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="96e74-173">Trasy przedstawione w powyższej tabeli można konfigurować za `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`pośrednictwem programu.</span><span class="sxs-lookup"><span data-stu-id="96e74-173">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="96e74-174">Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="96e74-174">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="96e74-175">W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security`.</span><span class="sxs-lookup"><span data-stu-id="96e74-175">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="96e74-176">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="96e74-176">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="96e74-177">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96e74-177">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="96e74-178">Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować `RemoteAuthenticatorView` parametr z jawnym akcją:</span><span class="sxs-lookup"><span data-stu-id="96e74-178">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="96e74-179">Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="96e74-179">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="96e74-180">Dostosowywanie interfejsu użytkownika uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="96e74-180">Customize the authentication user interface</span></span>

<span data-ttu-id="96e74-181">`RemoteAuthenticatorView`zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="96e74-181">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="96e74-182">Każdy stan można dostosować, przekazując element niestandardowe `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="96e74-182">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="96e74-183">Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić `RemoteAuthenticatorView` w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="96e74-183">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="96e74-184">`Authentication`składnik (*strony/uwierzytelnianie. Razor*):</span><span class="sxs-lookup"><span data-stu-id="96e74-184">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="96e74-185">`RemoteAuthenticatorView` Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96e74-185">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="96e74-186">Trasa</span><span class="sxs-lookup"><span data-stu-id="96e74-186">Route</span></span>                            | <span data-ttu-id="96e74-187">Fragment</span><span class="sxs-lookup"><span data-stu-id="96e74-187">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="96e74-188">Dostosowywanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="96e74-188">Customize the user</span></span>

<span data-ttu-id="96e74-189">Użytkownicy powiązana z aplikacją mogą być dostosowywać.</span><span class="sxs-lookup"><span data-stu-id="96e74-189">Users bound to the app can be customized.</span></span> <span data-ttu-id="96e74-190">W poniższym przykładzie Wszyscy uwierzytelnieni użytkownicy otrzymują `amr` każde z metod uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="96e74-190">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="96e74-191">Utwórz klasę rozszerzającą `RemoteUserAccount` klasę:</span><span class="sxs-lookup"><span data-stu-id="96e74-191">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="96e74-192">Utwórz fabrykę, która `AccountClaimsPrincipalFactory<TAccount>`rozszerza:</span><span class="sxs-lookup"><span data-stu-id="96e74-192">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public AccountClaimsPrincipalFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
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

<span data-ttu-id="96e74-193">Zarejestruj usługi, `CustomAccountFactory`aby użyć:</span><span class="sxs-lookup"><span data-stu-id="96e74-193">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="96e74-194">Obsługa prerenderowania przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="96e74-194">Support prerendering with authentication</span></span>

<span data-ttu-id="96e74-195">Po zastosowaniu wskazówek w jednej z aplikacji hostowanego Blazor zestawu webassembly Skorzystaj z poniższych instrukcji, aby utworzyć aplikację, która:</span><span class="sxs-lookup"><span data-stu-id="96e74-195">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="96e74-196">Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="96e74-196">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="96e74-197">Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="96e74-197">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="96e74-198">W `Program` klasie aplikacji klienckiej (*program.cs*) należy wykonać typowe rejestracje usługi w oddzielnym metodzie (na przykład `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="96e74-198">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
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

<span data-ttu-id="96e74-199">W aplikacji `Startup.ConfigureServices`serwera Zarejestruj następujące dodatkowe usługi:</span><span class="sxs-lookup"><span data-stu-id="96e74-199">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="96e74-200">W `Startup.Configure` metodzie aplikacji serwera Zastąp `endpoints.MapFallbackToFile("index.html")` ciąg opcją `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="96e74-200">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="96e74-201">W aplikacji serwer Utwórz folder *strony* , jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="96e74-201">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="96e74-202">Utwórz stronę *_Host. cshtml* w folderze *strony* aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="96e74-202">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="96e74-203">Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="96e74-203">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="96e74-204">Aktualizuj zawartość pliku:</span><span class="sxs-lookup"><span data-stu-id="96e74-204">Update the file's contents:</span></span>

* <span data-ttu-id="96e74-205">Dodaj `@page "_Host"` na początku pliku.</span><span class="sxs-lookup"><span data-stu-id="96e74-205">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="96e74-206">Zastąp `<app>Loading...</app>` tag następującym:</span><span class="sxs-lookup"><span data-stu-id="96e74-206">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="96e74-207">Opcje aplikacji hostowanych i dostawców logowania innych firm</span><span class="sxs-lookup"><span data-stu-id="96e74-207">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="96e74-208">Podczas uwierzytelniania i autoryzowania hostowanej Blazor aplikacji sieci webassembly przy użyciu dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="96e74-208">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="96e74-209">Wybór jednego z nich zależy od danego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="96e74-209">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="96e74-210">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="96e74-210">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="96e74-211">Uwierzytelnianie użytkowników tylko w celu wywołania chronionych interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="96e74-211">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="96e74-212">Uwierzytelnij użytkownika za pomocą przepływu OAuth po stronie klienta dla dostawcy interfejsu API innej firmy:</span><span class="sxs-lookup"><span data-stu-id="96e74-212">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="96e74-213">W tym scenariuszu:</span><span class="sxs-lookup"><span data-stu-id="96e74-213">In this scenario:</span></span>

* <span data-ttu-id="96e74-214">Serwer hostujący aplikację nie odgrywa roli.</span><span class="sxs-lookup"><span data-stu-id="96e74-214">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="96e74-215">Nie można chronić interfejsów API na serwerze.</span><span class="sxs-lookup"><span data-stu-id="96e74-215">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="96e74-216">Aplikacja może wywoływać tylko chronione interfejsy API innych firm.</span><span class="sxs-lookup"><span data-stu-id="96e74-216">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="96e74-217">Uwierzytelnianie użytkowników za pomocą dostawcy innych firm i wywoływanie chronionych interfejsów API na serwerze hosta i stronie trzeciej</span><span class="sxs-lookup"><span data-stu-id="96e74-217">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="96e74-218">Skonfiguruj tożsamość przy użyciu innego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="96e74-218">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="96e74-219">Uzyskaj tokeny wymagane przez dostęp do interfejsu API innych firm i Zapisz je.</span><span class="sxs-lookup"><span data-stu-id="96e74-219">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="96e74-220">Gdy użytkownik loguje się, tożsamość zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="96e74-220">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="96e74-221">W tym momencie istnieje kilka podejścia dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.</span><span class="sxs-lookup"><span data-stu-id="96e74-221">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="96e74-222">Korzystanie z tokenu dostępu do serwera w celu pobrania tokenu dostępu innej firmy</span><span class="sxs-lookup"><span data-stu-id="96e74-222">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="96e74-223">Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="96e74-223">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="96e74-224">Z tego miejsca Użyj tokenu dostępu innej firmy do wywołania zasobów interfejsu API innych firm bezpośrednio z tożsamości na kliencie.</span><span class="sxs-lookup"><span data-stu-id="96e74-224">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="96e74-225">Nie zalecamy tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="96e74-225">We don't recommend this approach.</span></span> <span data-ttu-id="96e74-226">Takie podejście wymaga traktowania tokenu dostępu innej firmy, tak jakby został wygenerowany dla klienta publicznego.</span><span class="sxs-lookup"><span data-stu-id="96e74-226">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="96e74-227">W przypadku postanowień uwierzytelniania OAuth publiczna aplikacja nie ma tajnego klienta, ponieważ nie może być zaufana do bezpiecznego przechowywania wpisów tajnych, a token dostępu jest generowany dla klienta poufnego.</span><span class="sxs-lookup"><span data-stu-id="96e74-227">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="96e74-228">Klient poufny jest klientem, który ma klucz tajny klienta i ma możliwość bezpiecznego przechowywania wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="96e74-228">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="96e74-229">Token dostępu innej firmy może mieć przyznane dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że firma zewnętrzna emituje token dla bardziej zaufanego klienta.</span><span class="sxs-lookup"><span data-stu-id="96e74-229">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="96e74-230">Podobnie tokeny odświeżania nie powinny być wystawiane dla klienta, który nie jest zaufany. w takim przypadku klient nie będzie miał nieograniczonego dostępu, chyba że zostaną zastosowane inne ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="96e74-230">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="96e74-231">Wykonywanie wywołań interfejsu API z klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm</span><span class="sxs-lookup"><span data-stu-id="96e74-231">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="96e74-232">Wykonaj wywołanie interfejsu API z klienta do interfejsu API serwera.</span><span class="sxs-lookup"><span data-stu-id="96e74-232">Make an API call from the client to the server API.</span></span> <span data-ttu-id="96e74-233">Na serwerze programu Pobierz token dostępu dla zasobu interfejsu API innej firmy i wystaw, w jaki sposób jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="96e74-233">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="96e74-234">Chociaż to podejście wymaga dodatkowego skoku sieci przez serwer w celu wywołania interfejsu API innej firmy, ostatecznie powoduje bezpieczniejsze środowisko:</span><span class="sxs-lookup"><span data-stu-id="96e74-234">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="96e74-235">Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.</span><span class="sxs-lookup"><span data-stu-id="96e74-235">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="96e74-236">Aplikacja nie może wyciekować tokenów dostępu z serwera, który może zawierać bardziej poufne uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="96e74-236">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
