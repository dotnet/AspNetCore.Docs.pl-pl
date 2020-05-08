---
title: Dodatkowe Blazor scenariusze zabezpieczeń ASP.NET Core webassembly
author: guardrex
description: Dowiedz się, Blazor jak skonfigurować zestaw webassembly pod kątem dodatkowych scenariuszy zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e804c43ebea8f6a79443e24047a7be47587cbd8a
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967549"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Dodatkowe scenariusze zabezpieczeń ASP.NET Core Blazor webassembly

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a>Dołącz tokeny do żądań wychodzących

`AuthorizationMessageHandler` Usługa może być używana z `HttpClient` programem w celu dołączania tokenów dostępu do żądań wychodzących. Tokeny są uzyskiwane przy `IAccessTokenProvider` użyciu istniejącej usługi. Jeśli nie można uzyskać tokenu, zgłaszany `AccessTokenNotAvailableException` jest wyjątek. `AccessTokenNotAvailableException`ma `Redirect` metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu. `AuthorizationMessageHandler` Można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL `ConfigureHandler` przy użyciu metody.

W poniższym `AuthorizationMessageHandler` przykładzie konfiguruje `HttpClient` w `Program.Main` (*program.cs*):

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

Dla wygody `BaseAddressAuthorizationMessageHandler` jest dołączony wstępnie skonfigurowany przy użyciu adresu podstawowego aplikacji jako autoryzowany adres URL. Szablony zestawów webassembly z włączonym uwierzytelnianiem Blazor <xref:System.Net.Http.IHttpClientFactory> teraz używane w projekcie interfejsu API serwera w celu <xref:System.Net.Http.HttpClient> skonfigurowania `BaseAddressAuthorizationMessageHandler`programu przy użyciu:

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

W przypadku, gdy klient został `CreateClient` utworzony za pomocą programu w powyższym przykładzie, <xref:System.Net.Http.HttpClient> jest dostarczane wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do projektu serwera.

Skonfigurowany <xref:System.Net.Http.HttpClient> jest następnie używany do autoryzowania żądań przy użyciu prostego `try-catch` wzorca. Następujący `FetchData` składnik żąda danych prognozy pogody:

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

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

Alternatywnie można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy:

*WeatherClient.cs*:

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

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

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

*Program.cs*:

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*FetchData. Razor*:

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a>Żądaj dodatkowych tokenów dostępu

Tokeny dostępu można uzyskać ręcznie, wywołując `IAccessTokenProvider.RequestAccessToken`metodę.

W poniższym przykładzie dodatkowe zakresy interfejsu API usługi Azure Active Directory (AAD) Microsoft Graph są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania wiadomości e-mail. Po dodaniu Microsoft Graph uprawnień interfejsu API w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienckiej (`Program.Main`, *program.cs*):

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

`IAccessTokenProvider.RequestToken` Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów, jak pokazano w następującym przykładzie:

```csharp
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

`TryGetToken`typu

* `true`z `token` do użycia.
* `false`Jeśli nie pobrano tokenu.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient i HttpRequestMessage za pomocą opcji żądania interfejsu API pobierania

W przypadku uruchamiania w zestawie webassembly w aplikacji Blazor webassembly [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań. Na przykład można określić metodę HTTP i nagłówki żądania. Poniższy przykład wykonuje `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:

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

Implementacja programu `HttpClient` .NET webassembly używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Opcje żądania pobrania HTTP można skonfigurować przy użyciu `HttpRequestMessage` metod rozszerzających pokazanych w poniższej tabeli.

| `HttpRequestMessage`Metoda rozszerzenia | Właściwość żądania pobrania |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [uwierzytelniające](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [Chow](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [wyst](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [spójn](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Można ustawić dodatkowe opcje przy użyciu bardziej generycznej `SetBrowserRequestOption` metody rozszerzenia.
 
Odpowiedź HTTP jest zwykle buforowana w aplikacji Blazor webassembly, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi. Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy `SetBrowserResponseStreamingEnabled` użyć metody rozszerzenia w żądaniu.

Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj metody `SetBrowserRequestCredentials` rozszerzenia:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Podczas wysyłania poświadczeń (plików cookie/nagłówki autoryzacji) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.

Następujące zasady obejmują konfigurację programu:

* Pochodzenie żądania (`http://localhost:5000`, `https://localhost:5001`).
* Dowolna Metoda (czasownik).
* `Content-Type`i `Authorization` nagłówki. Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header`), Wyświetl nagłówek podczas wywoływania. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>
* Poświadczenia ustawione przez kod JavaScript po stronie klienta (`credentials` Właściwość ustawiona na `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Aby uzyskać więcej informacji, <xref:security/cors> Zobacz i składnik Tester żądania HTTP aplikacji przykładowej (*Components/HTTPRequestTester. Razor*).

## <a name="handle-token-request-errors"></a>Obsługa błędów żądania tokenu

Gdy aplikacja jednostronicowa uwierzytelnia użytkownika za pomocą funkcji Open ID Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w postaci pliku cookie sesji, który jest ustawiany w wyniku użytkownika dostarczającego poświadczenia.

Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny. W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów. W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.

Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP. Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy. Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.

Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach. Są one częścią charakteru aplikacji jednostronicowych. SPA używający plików cookie również nie może wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania zostanie usunięty.

Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:

* Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.
* Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.

Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:

* Żądanie powiodło się, a aplikacja ma prawidłowy token.
* Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.

Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania. Istnieją różne podejścia z rosnącymi poziomami złożoności:

* Przechowuj bieżący stan strony w magazynie sesji. Przed `OnInitializeAsync`kontynuowaniem sprawdź, czy stan można przywrócić.
* Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.
* Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.

Poniższy przykład pokazuje, jak:

* Zachowaj stan przed przekierowaniem do strony logowania.
* Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Zapisz stan aplikacji przed operacją uwierzytelniania

Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP. Taka sytuacja może wystąpić w przypadku korzystania z takiego elementu jak kontenera stanu i przywrócenia stanu po pomyślnym uwierzytelnieniu. Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania.

`Authentication`składnik (*strony/uwierzytelnianie. Razor*):

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

## <a name="customize-app-routes"></a>Dostosowywanie tras aplikacji

Domyślnie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Biblioteka używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.

| Trasa                            | Przeznaczenie |
| -------------------------------- | ------- |
| `authentication/login`           | Wyzwala operację logowania. |
| `authentication/login-callback`  | Obsługuje wynik operacji logowania. |
| `authentication/login-failed`    | Wyświetla komunikaty o błędach, gdy operacja logowania zakończy się niepowodzeniem z jakiegoś powodu. |
| `authentication/logout`          | Wyzwala operację wylogowania. |
| `authentication/logout-callback` | Obsługuje wynik operacji wylogowania. |
| `authentication/logout-failed`   | Wyświetla komunikaty o błędach, gdy operacja wylogowania nie powiedzie się z jakiegoś powodu. |
| `authentication/logged-out`      | Wskazuje, że użytkownik pomyślnie wylogować się. |
| `authentication/profile`         | Wyzwala operację edytowania profilu użytkownika. |
| `authentication/register`        | Wyzwala operację w celu zarejestrowania nowego użytkownika. |

Trasy przedstawione w powyższej tabeli można konfigurować za `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`pośrednictwem programu. Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.

W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security`.

`Authentication`składnik (*strony/uwierzytelnianie. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować `RemoteAuthenticatorView` parametr z jawnym akcją:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.

## <a name="customize-the-authentication-user-interface"></a>Dostosowywanie interfejsu użytkownika uwierzytelniania

`RemoteAuthenticatorView`zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania. Każdy stan można dostosować, przekazując element niestandardowe `RenderFragment`. Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić `RemoteAuthenticatorView` w następujący sposób.

`Authentication`składnik (*strony/uwierzytelnianie. Razor*):

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

`RemoteAuthenticatorView` Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.

| Trasa                            | Fragment                |
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

## <a name="customize-the-user"></a>Dostosowywanie użytkownika

Użytkownicy powiązana z aplikacją mogą być dostosowywać. W poniższym przykładzie Wszyscy uwierzytelnieni użytkownicy otrzymują `amr` każde z metod uwierzytelniania użytkownika.

Utwórz klasę rozszerzającą `RemoteUserAccount` klasę:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Utwórz fabrykę, która `AccountClaimsPrincipalFactory<TAccount>`rozszerza:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
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

Zarejestruj usługi, `CustomAccountFactory`aby użyć:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a>Obsługa prerenderowania przy użyciu uwierzytelniania

Po zastosowaniu wskazówek w jednej z aplikacji hostowanego Blazor zestawu webassembly Skorzystaj z poniższych instrukcji, aby utworzyć aplikację, która:

* Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.
* Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.

W `Program` klasie aplikacji klienckiej (*program.cs*) należy wykonać typowe rejestracje usługi w oddzielnym metodzie (na przykład `ConfigureCommonServices`):

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

W aplikacji `Startup.ConfigureServices`serwera Zarejestruj następujące dodatkowe usługi:

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

W `Startup.Configure` metodzie aplikacji serwera Zastąp `endpoints.MapFallbackToFile("index.html")` ciąg opcją `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

W aplikacji serwer Utwórz folder *strony* , jeśli nie istnieje. Utwórz stronę *_Host. cshtml* w folderze *strony* aplikacji serwera. Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *pages/_Host. cshtml* . Aktualizuj zawartość pliku:

* Dodaj `@page "_Host"` na początku pliku.
* Zastąp `<app>Loading...</app>` tag następującym:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opcje aplikacji hostowanych i dostawców logowania innych firm

Podczas uwierzytelniania i autoryzowania hostowanej Blazor aplikacji sieci webassembly przy użyciu dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika. Wybór jednego z nich zależy od danego scenariusza.

Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Uwierzytelnianie użytkowników tylko w celu wywołania chronionych interfejsów API innych firm

Uwierzytelnij użytkownika za pomocą przepływu OAuth po stronie klienta dla dostawcy interfejsu API innej firmy:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 W tym scenariuszu:

* Serwer hostujący aplikację nie odgrywa roli.
* Nie można chronić interfejsów API na serwerze.
* Aplikacja może wywoływać tylko chronione interfejsy API innych firm.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Uwierzytelnianie użytkowników za pomocą dostawcy innych firm i wywoływanie chronionych interfejsów API na serwerze hosta i stronie trzeciej

Skonfiguruj Identity przy użyciu innego dostawcy logowania. Uzyskaj tokeny wymagane przez dostęp do interfejsu API innych firm i Zapisz je.

Gdy użytkownik loguje się, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania. W tym momencie istnieje kilka podejścia dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Korzystanie z tokenu dostępu do serwera w celu pobrania tokenu dostępu innej firmy

Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera. Z tego miejsca Użyj tokenu dostępu innej firmy do wywołania zasobów interfejsu API innych firm bezpośrednio z poziomu Identity klienta.

Nie zalecamy tego podejścia. Takie podejście wymaga traktowania tokenu dostępu innej firmy, tak jakby został wygenerowany dla klienta publicznego. W przypadku postanowień uwierzytelniania OAuth publiczna aplikacja nie ma tajnego klienta, ponieważ nie może być zaufana do bezpiecznego przechowywania wpisów tajnych, a token dostępu jest generowany dla klienta poufnego. Klient poufny jest klientem, który ma klucz tajny klienta i ma możliwość bezpiecznego przechowywania wpisów tajnych.

* Token dostępu innej firmy może mieć przyznane dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że firma zewnętrzna emituje token dla bardziej zaufanego klienta.
* Podobnie tokeny odświeżania nie powinny być wystawiane dla klienta, który nie jest zaufany. w takim przypadku klient nie będzie miał nieograniczonego dostępu, chyba że zostaną zastosowane inne ograniczenia.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Wykonywanie wywołań interfejsu API z klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm

Wykonaj wywołanie interfejsu API z klienta do interfejsu API serwera. Na serwerze programu Pobierz token dostępu dla zasobu interfejsu API innej firmy i wystaw, w jaki sposób jest wymagane.

Chociaż to podejście wymaga dodatkowego skoku sieci przez serwer w celu wywołania interfejsu API innej firmy, ostatecznie powoduje bezpieczniejsze środowisko:

* Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.
* Aplikacja nie może wyciekować tokenów dostępu z serwera, który może zawierać bardziej poufne uprawnienia.
