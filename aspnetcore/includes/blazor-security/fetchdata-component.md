<span data-ttu-id="ad19b-101">`FetchData`Składnik pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="ad19b-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="ad19b-102">Inicjowanie obsługi administracyjnej tokenu dostępu.</span><span class="sxs-lookup"><span data-stu-id="ad19b-102">Provision an access token.</span></span>
* <span data-ttu-id="ad19b-103">Użyj tokenu dostępu, aby wywołać interfejs API zasobów chronionych w aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="ad19b-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="ad19b-104">[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)Dyrektywa wskazuje system autoryzacji zestawu Webassembly Blazor, który użytkownik musi mieć autoryzację, aby móc odwiedzić ten składnik.</span><span class="sxs-lookup"><span data-stu-id="ad19b-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="ad19b-105">Obecność atrybutu w aplikacji *klienckiej* nie zapobiega WYWOŁYWANIU interfejsu API na serwerze bez poprawnych poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="ad19b-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="ad19b-106">Aplikacja *serwera* musi również użyć `[Authorize]` odpowiednich punktów końcowych, aby je poprawnie chronić.</span><span class="sxs-lookup"><span data-stu-id="ad19b-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="ad19b-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>wykonuje żądanie tokenu dostępu, który można dodać do żądania, aby wywołać interfejs API.</span><span class="sxs-lookup"><span data-stu-id="ad19b-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="ad19b-108">Jeśli token jest buforowany lub usługa jest w stanie zainicjować nowy token dostępu bez interakcji z użytkownikiem, żądanie tokenu powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ad19b-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="ad19b-109">W przeciwnym razie żądanie tokenu kończy się niepowodzeniem z <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , który jest przechwytywany w instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="ad19b-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="ad19b-110">Aby uzyskać rzeczywisty token do uwzględnienia w żądaniu, aplikacja musi sprawdzić, czy żądanie powiodło się, wywołując metodę [tokenResult. TryGetToken (token var)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span><span class="sxs-lookup"><span data-stu-id="ad19b-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [tokenResult.TryGetToken(out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="ad19b-111">Jeśli żądanie zakończyło się pomyślnie, zmienna tokenu jest wypełniana tokenem dostępu.</span><span class="sxs-lookup"><span data-stu-id="ad19b-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="ad19b-112"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>Właściwość tokenu ujawnia ciąg literału, który ma zostać uwzględniony w `Authorization` nagłówku żądania.</span><span class="sxs-lookup"><span data-stu-id="ad19b-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="ad19b-113">Jeśli żądanie nie powiodło się, ponieważ nie można zainicjować obsługi administracyjnej tokenu bez interakcji z użytkownikiem, wynik tokenu zawiera adres URL przekierowania.</span><span class="sxs-lookup"><span data-stu-id="ad19b-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="ad19b-114">Przechodzenie do tego adresu URL powoduje, że użytkownik jest zalogowany na stronie logowania i z powrotem do bieżącej strony po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="ad19b-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
