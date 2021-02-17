---
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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551591"
---
<span data-ttu-id="b99cd-101">`FetchData`Składnik pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="b99cd-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="b99cd-102">Inicjowanie obsługi administracyjnej tokenu dostępu.</span><span class="sxs-lookup"><span data-stu-id="b99cd-102">Provision an access token.</span></span>
* <span data-ttu-id="b99cd-103">Użyj tokenu dostępu, aby wywołać interfejs API zasobów chronionych w aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="b99cd-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="b99cd-104">[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)Dyrektywa wskazuje Blazor WebAssembly system autoryzacji, do którego użytkownik musi zostać autoryzowany w celu odwiedzenia tego składnika.</span><span class="sxs-lookup"><span data-stu-id="b99cd-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="b99cd-105">Obecność atrybutu w `Client` aplikacji nie zapobiega wywoływaniu interfejsu API na serwerze bez poprawnych poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="b99cd-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="b99cd-106">`Server`Aplikacja musi również użyć `[Authorize]` odpowiednich punktów końcowych, aby je poprawnie chronić.</span><span class="sxs-lookup"><span data-stu-id="b99cd-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="b99cd-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> wykonuje żądanie tokenu dostępu, który można dodać do żądania, aby wywołać interfejs API.</span><span class="sxs-lookup"><span data-stu-id="b99cd-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="b99cd-108">Jeśli token jest buforowany lub usługa jest w stanie zainicjować nowy token dostępu bez interakcji z użytkownikiem, żądanie tokenu powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="b99cd-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="b99cd-109">W przeciwnym razie żądanie tokenu kończy się niepowodzeniem z obiektem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , który jest przechwytywany w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji.</span><span class="sxs-lookup"><span data-stu-id="b99cd-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="b99cd-110">Aby uzyskać rzeczywisty token do uwzględnienia w żądaniu, aplikacja musi sprawdzić, czy żądanie powiodło się, wywołując metodę [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) .</span><span class="sxs-lookup"><span data-stu-id="b99cd-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="b99cd-111">Jeśli żądanie zakończyło się pomyślnie, zmienna tokenu jest wypełniana tokenem dostępu.</span><span class="sxs-lookup"><span data-stu-id="b99cd-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="b99cd-112"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>Właściwość tokenu ujawnia ciąg literału, który ma zostać uwzględniony w `Authorization` nagłówku żądania.</span><span class="sxs-lookup"><span data-stu-id="b99cd-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="b99cd-113">Jeśli żądanie nie powiodło się, ponieważ nie można zainicjować obsługi administracyjnej tokenu bez interakcji z użytkownikiem, wynik tokenu zawiera adres URL przekierowania.</span><span class="sxs-lookup"><span data-stu-id="b99cd-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="b99cd-114">Przechodzenie do tego adresu URL powoduje, że użytkownik jest zalogowany na stronie logowania i z powrotem do bieżącej strony po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="b99cd-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
