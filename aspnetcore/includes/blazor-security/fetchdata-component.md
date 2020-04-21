<span data-ttu-id="6e6d0-101">Składnik `FetchData` pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="6e6d0-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="6e6d0-102">Aprowizuj token dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-102">Provision an access token.</span></span>
* <span data-ttu-id="6e6d0-103">Użyj tokenu dostępu, aby wywołać chroniony interfejs API zasobów w aplikacji *Serwer.*</span><span class="sxs-lookup"><span data-stu-id="6e6d0-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="6e6d0-104">Dyrektywa `@attribute [Authorize]` wskazuje system autoryzacji Blazor WebAssembly, że użytkownik musi być autoryzowany, aby odwiedzić ten składnik.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="6e6d0-105">Obecność atrybutu w aplikacji *klienta* nie uniemożliwia wywoływania interfejsu API na serwerze bez odpowiednich poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="6e6d0-106">Aplikacja *Server* również `[Authorize]` należy użyć w odpowiednich punktach końcowych, aby poprawnie je chronić.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="6e6d0-107">`AuthenticationService.RequestAccessToken();`zajmuje się żądanie tokenu dostępu, który można dodać do żądania wywołania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="6e6d0-108">Jeśli token jest buforowany lub usługa jest w stanie aprowizować nowy token dostępu bez interakcji z użytkownikiem, żądanie tokenu powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="6e6d0-109">W przeciwnym razie żądanie tokenu kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="6e6d0-110">Aby uzyskać rzeczywisty token do uwzględnienia w żądaniu, aplikacja musi sprawdzić, czy żądanie powiodło się, wywołując `tokenResult.TryGetToken(out var token)`.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="6e6d0-111">Jeśli żądanie zakończyło się pomyślnie, zmienna tokenu jest wypełniona tokenem dostępu.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="6e6d0-112">Właściwość `Value` tokenu udostępnia ciąg literału do `Authorization` uwzględnienia w nagłówku żądania.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="6e6d0-113">Jeśli żądanie nie powiodło się, ponieważ token nie może być aprowizowana bez interakcji z użytkownikiem, wynik tokenu zawiera adres URL przekierowania.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="6e6d0-114">Przejście do tego adresu URL powoduje przejście użytkownika do strony logowania i powrót do bieżącej strony po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="6e6d0-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="6e6d0-115">Aby uzyskać więcej informacji, zobacz [Zapisywanie stanu aplikacji przed operacją uwierzytelniania](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="6e6d0-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
