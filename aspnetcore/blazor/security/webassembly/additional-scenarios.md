---
title: ASP.NET Core Blazor WebAssembly dodatkowe scenariusze zabezpieczeń
author: guardrex
description: Dowiedz się, jak skonfigurować Blazor WebAssembly dodatkowe scenariusze zabezpieczeń.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: bb502533bca24e82792db8814b75b16407f20339
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870389"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly dodatkowe scenariusze zabezpieczeń

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Dołącz tokeny do żądań wychodzących

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> jest <xref:System.Net.Http.DelegatingHandler> używany do dołączania tokenów dostępu do <xref:System.Net.Http.HttpResponseMessage> wystąpień wychodzących. Tokeny są uzyskiwane przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> usługi, która jest rejestrowana przez platformę. Jeśli nie można uzyskać tokenu, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> zgłaszany jest wyjątek. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ma <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodę, która może być używana do nawigowania do dostawcy tożsamości w celu uzyskania nowego tokenu.

Dla wygody, struktura zapewnia <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> wstępnie skonfigurowany adres podstawowy aplikacji jako autoryzowany adres URL. **Tokeny dostępu są dodawane tylko wtedy, gdy identyfikator URI żądania jest w podstawowym identyfikatorze URI aplikacji.** Gdy identyfikatory URI żądania wychodzącego nie znajdują się w podstawowym identyfikatorze URI aplikacji, użyj [niestandardowej `AuthorizationMessageHandler` klasy (*zalecane*)](#custom-authorizationmessagehandler-class) lub [Skonfiguruj `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).

> [!NOTE]
> Oprócz konfiguracji aplikacji klienta dostępu do interfejsu API serwera, interfejs API serwera musi również zezwalać na żądania między źródłami (CORS), gdy klient i serwer nie znajdują się na tym samym adresie podstawowym. Aby uzyskać więcej informacji na temat konfiguracji funkcji CORS po stronie serwera, zobacz sekcję [udostępnianie zasobów między źródłami (CORS)](#cross-origin-resource-sharing-cors) w dalszej części tego artykułu.

W poniższym przykładzie:

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> dodaje <xref:System.Net.Http.IHttpClientFactory> i pokrewnych usług do kolekcji usług i konfiguruje nazwę <xref:System.Net.Http.HttpClient> ( `ServerAPI` ). <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> jest adresem podstawowym identyfikatora URI zasobu podczas wysyłania żądań. <xref:System.Net.Http.IHttpClientFactory> jest dostarczany przez [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakiet NuGet.
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> jest <xref:System.Net.Http.DelegatingHandler> używany do dołączania tokenów dostępu do <xref:System.Net.Http.HttpResponseMessage> wystąpień wychodzących. Tokeny dostępu są dodawane tylko wtedy, gdy identyfikator URI żądania jest w podstawowym identyfikatorze URI aplikacji.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> tworzy i konfiguruje <xref:System.Net.Http.HttpClient> wystąpienie dla żądań wychodzących przy użyciu konfiguracji, która odnosi się do nazwy <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).

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

W przypadku Blazor aplikacji na podstawie Blazor WebAssembly szablonu hostowanego projektu identyfikatory URI żądania są domyślnie w podstawowym identyfikatorze URI aplikacji. W związku z tym <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> elementu w aplikacji wygenerowanej na podstawie szablonu projektu.

Skonfigurowana konfiguracja służy <xref:System.Net.Http.HttpClient> do podejmowania autoryzowanych żądań przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) wzorca:

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Http.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a>Klasa niestandardowa `AuthorizationMessageHandler`

*Te wskazówki w tej sekcji są zalecane dla aplikacji klienckich, które wysyłają żądania wychodzące do identyfikatorów URI, które nie znajdują się w podstawowym identyfikatorze URI aplikacji.*

W poniższym przykładzie Klasa niestandardowa rozszerza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> się do użycia jako <xref:System.Net.Http.DelegatingHandler> dla elementu <xref:System.Net.Http.HttpClient> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguruje ten program obsługi do autoryzacji wychodzących żądań HTTP przy użyciu tokenu dostępu. Token dostępu jest dołączany tylko wtedy, gdy co najmniej jeden z autoryzowanych adresów URL jest podstawą identyfikatora URI żądania ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).

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

W programie `Program.Main` ( `Program.cs` ) program `CustomAuthorizationMessageHandler` jest zarejestrowany jako usługa o określonym zakresie i jest skonfigurowany jako <xref:System.Net.Http.DelegatingHandler> dla wystąpień wychodzących <xref:System.Net.Http.HttpResponseMessage> wykonanych przez nazwę <xref:System.Net.Http.HttpClient> :

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

W przypadku Blazor aplikacji opartej na Blazor WebAssembly szablonie projektu hostowanego <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest domyślnie przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .

Skonfigurowany <xref:System.Net.Http.HttpClient> jest używany do autoryzacji żądań za pomocą [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) wzorca. Gdy klient jest tworzony przy użyciu <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakietu (pakiet), <xref:System.Net.Http.HttpClient> jest podano wystąpienia, które zawierają tokeny dostępu podczas wykonywania żądań do interfejsu API serwera. Jeśli identyfikator URI żądania jest względnym identyfikatorem URI, tak jak w poniższym przykładzie ( `ExampleAPIMethod` ), jest on połączony z, <xref:System.Net.Http.HttpClient.BaseAddress> gdy aplikacja kliencka wysyła żądanie:

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
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a>Ponowne `AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> można skonfigurować z autoryzowanymi adresami URL, zakresami i zwrotnym adresem URL przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metody. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguruje procedurę obsługi autoryzacji wychodzących żądań HTTP przy użyciu tokenu dostępu. Token dostępu jest dołączany tylko wtedy, gdy co najmniej jeden z autoryzowanych adresów URL jest podstawą identyfikatora URI żądania ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ). Jeśli identyfikator URI żądania jest względnym identyfikatorem URI, jest on połączony z <xref:System.Net.Http.HttpClient.BaseAddress> .

W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> konfiguruje <xref:System.Net.Http.HttpClient> w `Program.Main` ( `Program.cs` ):

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

W przypadku Blazor aplikacji na podstawie Blazor WebAssembly szablonu hostowanego projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> domyślnie przypisywany jest następujący element:

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Adres URL `authorizedUrls` tablicy.

## <a name="typed-httpclient"></a>Wpisane `HttpClient`

Można zdefiniować klienta z określonym typem, który obsługuje wszystkie problemy dotyczące pozyskiwania HTTP i tokenu w ramach jednej klasy.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient http;
 
    public WeatherForecastClient(HttpClient http)
    {
        this.http = http;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
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

Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `using static BlazorSample.Data;` ).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

W przypadku Blazor aplikacji opartej na Blazor WebAssembly szablonie projektu hostowanego <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest domyślnie przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .

`FetchData` składnik ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Skonfiguruj `HttpClient` procedurę obsługi

Program obsługi można skonfigurować w taki sposób, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> Aby wychodzące żądania HTTP.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

W przypadku Blazor aplikacji na podstawie Blazor WebAssembly szablonu hostowanego projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> domyślnie przypisywany jest następujący element:

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Adres URL `authorizedUrls` tablicy.

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym

Jeśli Blazor WebAssembly aplikacja zwykle korzysta z bezpiecznego ustawienia domyślnego <xref:System.Net.Http.HttpClient> , aplikacja może również wykonać nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web przez skonfigurowanie nazwanego elementu <xref:System.Net.Http.HttpClient> :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

W przypadku Blazor aplikacji opartej na Blazor WebAssembly szablonie projektu hostowanego <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) jest domyślnie przypisany do <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .

Poprzednia rejestracja jest uzupełnieniem istniejącej bezpiecznej rejestracji domyślnej <xref:System.Net.Http.HttpClient> .

Składnik tworzy <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakiet), aby wykonać nieuwierzytelnione lub nieautoryzowane żądania:

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
> Kontroler w interfejsie API serwera, `WeatherForecastNoAuthenticationController` dla poprzedniego przykładu, nie jest oznaczony [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem.

Decyzja o konieczności użycia bezpiecznego klienta lub niezabezpieczonego klienta jako <xref:System.Net.Http.HttpClient> wystąpienia domyślnego do dewelopera. Jednym ze sposobów podjęcia tej decyzji jest uwzględnienie liczby uwierzytelnionych i nieuwierzytelnionych punktów końcowych, z którymi kontaktuje się aplikacja. Jeśli większość żądań aplikacji jest zabezpieczonych punktów końcowych interfejsu API, użyj uwierzytelnionego <xref:System.Net.Http.HttpClient> wystąpienia jako domyślnego. W przeciwnym razie Zarejestruj nieuwierzytelnione <xref:System.Net.Http.HttpClient> wystąpienie jako domyślne.

Alternatywnym podejściem do korzystania z programu <xref:System.Net.Http.IHttpClientFactory> jest utworzenie [klienta o określonym typie](#typed-httpclient) dla nieuwierzytelnionego dostępu do anonimowych punktów końcowych.

## <a name="request-additional-access-tokens"></a>Żądaj dodatkowych tokenów dostępu

Tokeny dostępu można uzyskać ręcznie, wywołując metodę `IAccessTokenProvider.RequestAccessToken` . W poniższym przykładzie dodatkowy zakres jest wymagany przez aplikację domyślną <xref:System.Net.Http.HttpClient> . Przykład biblioteki Microsoft Authentication Library (MSAL) konfiguruje zakres przy użyciu `MsalProviderOptions` :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` Symbole zastępcze w powyższym przykładzie są zakresami niestandardowymi.

`IAccessTokenProvider.RequestToken`Metoda zapewnia Przeciążenie, które umożliwia aplikacji udostępnianie tokenu dostępu z danym zestawem zakresów.

W Razor składniku:

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` Symbole zastępcze w powyższym przykładzie są zakresami niestandardowymi.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> typu

* `true` z `token` do użycia.
* `false` Jeśli nie pobrano tokenu.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

W przypadku wysyłania poświadczeń ( cookie nagłówki autoryzacji/nagłówków) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.

Następujące zasady obejmują konfigurację programu:

* Pochodzenie żądania ( `http://localhost:5000` , `https://localhost:5001` ).
* Dowolna Metoda (czasownik).
* `Content-Type` i `Authorization` nagłówki. Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header` ), Wyświetl nagłówek podczas wywoływania <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Poświadczenia ustawione przez kod JavaScript po stronie klienta ( `credentials` Właściwość ustawiona na `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Rozwiązanie hostowane Blazor oparte na Blazor szablonie projektu hostowanego używa tego samego adresu podstawowego dla aplikacji klienta i serwera. Aplikacja kliencka <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> jest domyślnie ustawiona na identyfikator URI `builder.HostEnvironment.BaseAddress` . Konfiguracja mechanizmu CORS **nie** jest wymagana w domyślnej konfiguracji hostowanej aplikacji utworzonej na podstawie Blazor szablonu hostowanego projektu. Dodatkowe aplikacje klienckie, które nie są hostowane przez projekt serwera **i nie współdzielą adresu podstawowego** aplikacji serwera, wymagają konfiguracji CORS w projekcie serwera.

Aby uzyskać więcej informacji, zobacz <xref:security/cors> i składnik Tester żądania HTTP aplikacji przykładowej ( `Components/HTTPRequestTester.razor` ).

## <a name="handle-token-request-errors"></a>Obsługa błędów żądania tokenu

Gdy aplikacja jednostronicowa uwierzytelnia użytkownika przy użyciu usługi OpenID Connect Connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w ramach SPA i w Identity postaci sesji cookie , która jest ustawiona jako wynik użytkownika dostarczającego poświadczenia.

Tokeny, które są emitowane przez protokół IP dla użytkownika zwykle są ważne przez krótki okresy czasu, na ogół o godzinę, więc aplikacja kliencka musi regularnie pobierać nowe tokeny. W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów. W większości przypadków klienci OIDC mogą udostępniać nowe tokeny, nie wymagając ponownego uwierzytelnienia użytkownika w ramach stanu uwierzytelniania lub "sesji", który jest przechowywany w ramach adresu IP.

Istnieją sytuacje, w których klient nie może uzyskać tokenu bez interakcji użytkownika, na przykład gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP. Ten scenariusz występuje, gdy użytkownik odwiedzi `https://login.microsoftonline.com` i wyloguje się. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wyloguje się. Każdy token przechowywany przez klienta może już nie być prawidłowy. Ponadto klient nie może zainicjować obsługi nowego tokenu bez interakcji użytkownika po wygaśnięciu bieżącego tokenu.

Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenach. Są one częścią charakteru aplikacji jednostronicowych. SPA używa cookie również protokołu API serwera, jeśli uwierzytelnianie cookie zostało usunięte.

Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących kwestiach:

* Aby zainicjować obsługę nowego tokenu dostępu w celu wywołania interfejsu API, może być konieczne ponowne uwierzytelnienie użytkownika.
* Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie do serwera może się nie powieść, ponieważ token został odwołany przez użytkownika.

Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:

* Żądanie powiodło się, a aplikacja ma prawidłowy token.
* Żądanie nie powiedzie się, a aplikacja musi ponownie uwierzytelnić użytkownika w celu uzyskania nowego tokenu.

Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed przeprowadzeniem przekierowania. Istnieją różne podejścia z rosnącymi poziomami złożoności:

* Przechowuj bieżący stan strony w magazynie sesji. Podczas [ `OnInitializedAsync` zdarzenia cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) Sprawdź, czy stan można przywrócić przed kontynuowaniem.
* Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, którą potrzebuje do ponownego zapisu wcześniej zapisanego stanu.
* Dodaj parametr ciągu zapytania z unikatowym identyfikatorem w celu przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.

Poniższy przykład pokazuje, jak:

* Zachowaj stan przed przekierowaniem do strony logowania.
* Odzyskaj poprzedni stan, a następnie Uwierzytelnij przy użyciu parametru ciągu zapytania.

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider
...

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
        var http = new HttpClient();
        http.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            http.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await http.PostAsJsonAsync("Save", User);
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

Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji przed przekierowaniem przeglądarki do adresu IP. Może to być przypadek, gdy używasz kontenera stanu i chcesz przywrócić stan po pomyślnym uwierzytelnieniu. Możesz użyć niestandardowego obiektu stanu uwierzytelniania, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego, a następnie przywrócić ten stan po pomyślnym ukończeniu operacji uwierzytelniania. Poniższy przykład demonstruje podejście.

W aplikacji jest tworzona Klasa kontenera stanu z właściwościami do przechowywania wartości stanu aplikacji. W poniższym przykładzie kontener jest używany do utrzymania wartości licznika domyślnego składnika szablonu projektu `Counter` ( `Pages/Counter.razor` ). Metody serializacji i deserializacji kontenera są oparte na <xref:System.Text.Json> .

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

`Counter`Składnik używa kontenera stanu, aby zachować `currentCount` wartość spoza składnika:

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

Utwórz element `ApplicationAuthenticationState` z <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> . Podaj `Id` Właściwość, która służy jako identyfikator dla stanu przechowywanego lokalnie.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

`Authentication`Składnik ( `Pages/Authentication.razor` ) zapisuje i przywraca stan aplikacji za pomocą lokalnego magazynu sesji przy użyciu `StateContainer` metod serializacji i deserializacji `GetStateForLocalStorage` oraz `SetStateFromLocalStorage` :

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

Ten przykład używa Azure Active Directory (AAD) do uwierzytelniania. W `Program.Main` ( `Program.cs` ):

* `ApplicationAuthenticationState`Jest skonfigurowany jako typ biblioteki Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` .
* Kontener stanu jest zarejestrowany w kontenerze usługi.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Dostosowywanie tras aplikacji

Domyślnie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) Biblioteka używa tras przedstawionych w poniższej tabeli w celu reprezentowania różnych stanów uwierzytelniania.

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

Trasy przedstawione w powyższej tabeli można konfigurować za pośrednictwem programu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . Podczas ustawiania opcji w celu zapewnienia tras niestandardowych upewnij się, że aplikacja ma trasę obsługującą każdą ścieżkę.

W poniższym przykładzie wszystkie ścieżki są poprzedzone prefiksem `/security` .

`Authentication` składnik ( `Pages/Authentication.razor` ):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Jeśli wymaganie wywołuje całkowicie różne ścieżki, należy ustawić trasy zgodnie z wcześniejszym opisem i renderować <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> parametr z jawnym akcją:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Jeśli zdecydujesz się to zrobić, możesz przerwać interfejs użytkownika na różnych stronach.

## <a name="customize-the-authentication-user-interface"></a>Dostosowywanie interfejsu użytkownika uwierzytelniania

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania. Każdy stan można dostosować, przekazując element niestandardowe <xref:Microsoft.AspNetCore.Components.RenderFragment> . Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> następujący sposób.

`Authentication` składnik ( `Pages/Authentication.razor` ):

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Ma jeden fragment, którego można użyć na trasę uwierzytelniania pokazanym w poniższej tabeli.

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

Użytkownicy powiązana z aplikacją mogą być dostosowywać.

### <a name="customize-the-user-with-a-payload-claim"></a>Dostosowywanie użytkownika przy użyciu roszczeń ładunku

W poniższym przykładzie uwierzytelniani użytkownicy aplikacji otrzymują `amr` wierzytelność dla każdej metody uwierzytelniania użytkownika. `amr`Oświadczenie identyfikuje sposób uwierzytelniania podmiotu tokenu w Identity [oświadczeniach ładunku](/azure/active-directory/develop/access-tokens#the-amr-claim)platformy Microsoft Platform v 1.0. W przykładzie zastosowano niestandardową klasę konta użytkownika opartą na <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .

Utwórz klasę rozszerzającą <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> klasę. Poniższy przykład ustawia `AuthenticationMethod` Właściwość na tablicę `amr` wartości właściwości JSON. `AuthenticationMethod` jest automatycznie wypełniana przez platformę, gdy użytkownik jest uwierzytelniany.

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Utwórz fabrykę, która wykracza <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> na tworzenie oświadczeń z metod uwierzytelniania użytkownika przechowywanych w `CustomUserAccount.AuthenticationMethod` :

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

Zarejestruj `CustomAccountFactory` dostawcę uwierzytelniania w użyciu. Wszystkie następujące rejestracje są prawidłowe:

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a>Grupy zabezpieczeń i role usługi AAD z klasą niestandardowego konta użytkownika

Aby uzyskać dodatkowy przykład, który działa z grupami zabezpieczeń usługi AAD i rolami administratora usługi AAD oraz klasą niestandardowego konta użytkownika, zobacz <xref:blazor/security/webassembly/aad-groups-roles> .

## <a name="support-prerendering-with-authentication"></a>Obsługa prerenderowania przy użyciu uwierzytelniania

Po zastosowaniu wskazówek w jednej z Blazor WebAssembly tematów aplikacji hostowanych należy wykonać następujące instrukcje, aby utworzyć aplikację, która:

* Wstępnie renderuje ścieżki, dla których autoryzacja nie jest wymagana.
* Nie określa ścieżek PreRender, dla których wymagana jest autoryzacja.

W *`Client`* `Program` klasie aplikacji ( `Program.cs` ), typowe rejestracje usługi w ramach programu w oddzielnym metodzie (na przykład `ConfigureCommonServices` ):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add...;

        builder.Services.AddScoped( ... );

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

W aplikacji serwera `Startup.ConfigureServices` zarejestruj następujące dodatkowe usługi:

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

W metodzie aplikacji serwera `Startup.Configure` Zastąp ciąg [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) opcją [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

W aplikacji serwer Utwórz `Pages` folder, jeśli nie istnieje. Utwórz `_Host.cshtml` stronę w folderze aplikacji serwera `Pages` . Wklej zawartość z *`Client`* `wwwroot/index.html` pliku aplikacji do `Pages/_Host.cshtml` pliku. Aktualizuj zawartość pliku:

::: moniker range=">= aspnetcore-5.0"

* Dodaj `@page "_Host"` na początku pliku.
* Zastąp `<div id="app">Loading...</div>` tag następującym:

  ```cshtml
  <div id="app">
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </div>
  ```
  
  W poprzednim przykładzie symbol zastępczy `{CLIENT APP ASSEMBLY NAME}` jest nazwą zestawu aplikacji klienta (na przykład `BlazorSample.Client` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Dodaj `@page "_Host"` na początku pliku.
* Zastąp `<app>Loading...</app>` tag następującym:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
  W poprzednim przykładzie symbol zastępczy `{CLIENT APP ASSEMBLY NAME}` jest nazwą zestawu aplikacji klienta (na przykład `BlazorSample.Client` ).

::: moniker-end
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opcje aplikacji hostowanych i dostawców logowania innych firm

Podczas uwierzytelniania i autoryzowania hostowanej Blazor WebAssembly aplikacji za pomocą dostawcy innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika. Wybór jednego z nich zależy od danego scenariusza.

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

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Korzystanie z punktów końcowych OpenID Connect Connect (OIDC) v 2.0

Biblioteka uwierzytelniania i Blazor Szablony projektów korzystają z punktów końcowych OpenID Connect Connect (OIDC) v 1.0. Aby użyć punktu końcowego v 2.0, skonfiguruj opcję okaziciela JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> . W poniższym przykładzie skonfigurowano usługi AAD dla programu v 2.0 przez dołączenie `v2.0` segmentu do <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Właściwości:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Alternatywnie można wprowadzić to ustawienie w pliku ustawień aplikacji ( `appsettings.json` ):

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Jeśli znakowanie w segmencie urzędu nie jest odpowiednie dla dostawcy OIDC aplikacji, na przykład z dostawcami spoza usługi AAD, ustaw <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> Właściwość bezpośrednio. Ustaw właściwość w <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> lub w pliku ustawień aplikacji ( `appsettings.json` ) przy użyciu `Authority` klucza.

Lista oświadczeń w tokenie identyfikatora zmienia się dla punktów końcowych v 2.0. Aby uzyskać więcej informacji, zobacz [Dlaczego warto zaktualizować platformę tożsamości firmy Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).

## <a name="configure-and-use-grpc-in-components"></a>Konfigurowanie i używanie gRPC w składnikach

Aby skonfigurować Blazor WebAssembly aplikację do używania [ASP.NET Core gRPC Framework](xref:grpc/index):

* Włącz gRPC-Web na serwerze. Aby uzyskać więcej informacji, zobacz <xref:grpc/browser>.
* Zarejestruj usługi gRPC dla programu obsługi komunikatów aplikacji. Poniższy przykład konfiguruje procedurę obsługi komunikatów autoryzacji aplikacji do korzystania [ `GreeterClient` z usługi z samouczka gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):

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

Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample` ). Umieść `.proto` plik w `Shared` projekcie rozwiązania hostowanego Blazor .

Składnik w aplikacji klienckiej może wykonywać wywołania gRPC przy użyciu klienta gRPC ( `Pages/Grpc.razor` ):

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

Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample` ). Aby użyć `Status.DebugException` właściwości, użyj [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) w wersji 2.30.0 lub nowszej.

Aby uzyskać więcej informacji, zobacz <xref:grpc/browser>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/security/webassembly/graph-api>
* [`HttpClient` i `HttpRequestMessage` z opcjami żądania interfejsu API pobierania](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
