---
title: ASP.NET podstawowe Blazor scenariusze zabezpieczeń WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661593"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="0576a-102">ASP.NET Core Blazor WebAssembly dodatkowe scenariusze zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="0576a-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="0576a-103">Przez [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0576a-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="0576a-104">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="0576a-104">Request additional access tokens</span></span>

<span data-ttu-id="0576a-105">Większość aplikacji wymaga tylko tokenu dostępu do interakcji z chronionymi zasobami, których używają.</span><span class="sxs-lookup"><span data-stu-id="0576a-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="0576a-106">W niektórych scenariuszach aplikacja może wymagać więcej niż jednego tokenu w celu interakcji z dwoma lub więcej zasobów.</span><span class="sxs-lookup"><span data-stu-id="0576a-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="0576a-107">W poniższym przykładzie dodatkowe zakresy interfejsu API programu Microsoft Graph usługi Azure Active Directory (AAD) są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania poczty.</span><span class="sxs-lookup"><span data-stu-id="0576a-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="0576a-108">Po dodaniu uprawnień interfejsu API programu Microsoft Graph w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienta (`Program.Main`, *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0576a-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="0576a-109">Metoda `IAccessTokenProvider.RequestToken` zapewnia przeciążenie, które umożliwia aplikacji aprowizować token z danym zestawem zakresów, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0576a-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="0576a-110">`TryGetToken`Zwraca:</span><span class="sxs-lookup"><span data-stu-id="0576a-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="0576a-111">`true`z `token` do użytku.</span><span class="sxs-lookup"><span data-stu-id="0576a-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="0576a-112">`false`jeśli token nie jest pobierany.</span><span class="sxs-lookup"><span data-stu-id="0576a-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="0576a-113">Obsługa błędów żądań tokenu</span><span class="sxs-lookup"><span data-stu-id="0576a-113">Handle token request errors</span></span>

<span data-ttu-id="0576a-114">Gdy aplikacja jednostronicowa (SPA) uwierzytelnia użytkownika przy użyciu open id connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w spa i w dostawcy tożsamości (IP) w postaci pliku cookie sesji, który jest ustawiany w wyniku podania przez użytkownika swoich poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="0576a-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="0576a-115">Tokeny, które adres IP emituje dla użytkownika zazwyczaj są ważne przez krótki okres czasu, około jednej godziny normalnie, więc aplikacja kliencka musi regularnie pobierać nowe tokeny.</span><span class="sxs-lookup"><span data-stu-id="0576a-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="0576a-116">W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów.</span><span class="sxs-lookup"><span data-stu-id="0576a-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="0576a-117">W większości przypadków klienci OIDC są w stanie aprowizować nowe tokeny bez konieczności ponownego uwierzytelniania użytkownika za pomocą stanu uwierzytelniania lub "sesji", która jest przechowywana w adresie IP.</span><span class="sxs-lookup"><span data-stu-id="0576a-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="0576a-118">Istnieją przypadki, w których klient nie może uzyskać tokenu bez interakcji z użytkownikiem, na przykład, gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP.</span><span class="sxs-lookup"><span data-stu-id="0576a-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="0576a-119">Ten scenariusz występuje, `https://login.microsoftonline.com` jeśli użytkownik odwiedza i wylogowuje. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wylogował się. Każdy token, który przechowuje klient może nie być już prawidłowy.</span><span class="sxs-lookup"><span data-stu-id="0576a-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="0576a-120">Ponadto klient nie jest w stanie aprowizować nowego tokenu bez interakcji z użytkownikiem po wygaśnięciu bieżącego tokenu.</span><span class="sxs-lookup"><span data-stu-id="0576a-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="0576a-121">Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenie.</span><span class="sxs-lookup"><span data-stu-id="0576a-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="0576a-122">Są one częścią charakteru OSO.</span><span class="sxs-lookup"><span data-stu-id="0576a-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="0576a-123">Spa przy użyciu plików cookie również nie można wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="0576a-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="0576a-124">Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących czynnościach:</span><span class="sxs-lookup"><span data-stu-id="0576a-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="0576a-125">Aby aprowizować nowy token dostępu do wywołania interfejsu API, użytkownik może być zobowiązany do ponownego uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="0576a-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="0576a-126">Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie serwera może zakończyć się niepowodzeniem, ponieważ token został odwołany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0576a-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="0576a-127">Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:</span><span class="sxs-lookup"><span data-stu-id="0576a-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="0576a-128">Żądanie zakończy się pomyślnie, a aplikacja ma prawidłowy token.</span><span class="sxs-lookup"><span data-stu-id="0576a-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="0576a-129">Żądanie kończy się niepowodzeniem, a aplikacja musi ponownie uwierzytelnić użytkownika, aby uzyskać nowy token.</span><span class="sxs-lookup"><span data-stu-id="0576a-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="0576a-130">Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed wykonaniem przekierowania.</span><span class="sxs-lookup"><span data-stu-id="0576a-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="0576a-131">Istnieje kilka podejść z rosnącym poziomem złożoności:</span><span class="sxs-lookup"><span data-stu-id="0576a-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="0576a-132">Przechowuj bieżący stan strony w magazynie sesji.</span><span class="sxs-lookup"><span data-stu-id="0576a-132">Store the current page state in session storage.</span></span> <span data-ttu-id="0576a-133">Podczas `OnInitializeAsync`, sprawdź, czy stan można przywrócić przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="0576a-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="0576a-134">Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, że musi ponownie nawodnić wcześniej zapisany stan.</span><span class="sxs-lookup"><span data-stu-id="0576a-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="0576a-135">Dodaj parametr ciągu zapytania z unikatowym identyfikatorem do przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.</span><span class="sxs-lookup"><span data-stu-id="0576a-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="0576a-136">W poniższym przykładzie pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="0576a-136">The following example shows how to:</span></span>

* <span data-ttu-id="0576a-137">Zachowaj stan przed przekierowaniem do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="0576a-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="0576a-138">Odzyskaj poprzedni stan po uwierzytelnieniu przy użyciu parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="0576a-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="0576a-139">Zapisywanie stanu aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0576a-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="0576a-140">Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji, zanim przeglądarka zostanie przekierowana do adresu IP.</span><span class="sxs-lookup"><span data-stu-id="0576a-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="0576a-141">Może to być w przypadku, gdy używasz coś takiego jak kontener stanu i chcesz przywrócić stan po pomyślnym uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="0576a-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="0576a-142">Niestandardowego obiektu stanu uwierzytelniania można użyć, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego i przywrócić ten stan po pomyślnym zakończeniu operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0576a-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="0576a-143">`Authentication`składnik *(Pages/Authentication.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="0576a-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="0576a-144">Dostosowywanie tras aplikacji</span><span class="sxs-lookup"><span data-stu-id="0576a-144">Customize app routes</span></span>

<span data-ttu-id="0576a-145">Domyślnie biblioteka `Microsoft.AspNetCore.Components.WebAssembly.Authentication` używa tras wyświetlanych w poniższej tabeli do reprezentowania różnych stanów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0576a-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="0576a-146">Trasa</span><span class="sxs-lookup"><span data-stu-id="0576a-146">Route</span></span>                            | <span data-ttu-id="0576a-147">Przeznaczenie</span><span class="sxs-lookup"><span data-stu-id="0576a-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="0576a-148">Wyzwala operację logowania.</span><span class="sxs-lookup"><span data-stu-id="0576a-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="0576a-149">Obsługuje wynik każdej operacji logowania.</span><span class="sxs-lookup"><span data-stu-id="0576a-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="0576a-150">Wyświetla komunikaty o błędach, gdy operacja logowania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="0576a-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="0576a-151">Wyzwala operację wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="0576a-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="0576a-152">Obsługuje wynik operacji wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="0576a-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="0576a-153">Wyświetla komunikaty o błędach, gdy operacja wylogowywania nie powiedzie się z jakiegoś powodu.</span><span class="sxs-lookup"><span data-stu-id="0576a-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="0576a-154">Wskazuje, że użytkownik pomyślnie wylogował się.</span><span class="sxs-lookup"><span data-stu-id="0576a-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="0576a-155">Wyzwala operację, aby edytować profil użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0576a-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="0576a-156">Wyzwala operację, aby zarejestrować nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0576a-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="0576a-157">Trasy pokazane w powyższej tabeli `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`można konfigurować za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="0576a-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="0576a-158">Podczas ustawiania opcji, aby zapewnić trasy niestandardowe, upewnij się, że aplikacja ma trasę, która obsługuje każdą ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="0576a-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="0576a-159">W poniższym przykładzie wszystkie ścieżki są `/security`poprzedzone programem .</span><span class="sxs-lookup"><span data-stu-id="0576a-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="0576a-160">`Authentication`składnik *(Pages/Authentication.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="0576a-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="0576a-161">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0576a-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0576a-162">Jeśli wymaganie wymaga zupełnie innych ścieżek, ustaw trasy zgodnie `RemoteAuthenticatorView` z opisem wcześniej i renderuj z jawnym parametrem akcji:</span><span class="sxs-lookup"><span data-stu-id="0576a-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="0576a-163">Jeśli zdecydujesz się to zrobić, możesz podzielić interfejs użytkownika na różne strony.</span><span class="sxs-lookup"><span data-stu-id="0576a-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="0576a-164">Dostosowywanie interfejsu użytkownika uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0576a-164">Customize the authentication user interface</span></span>

<span data-ttu-id="0576a-165">`RemoteAuthenticatorView`zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0576a-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="0576a-166">Każdy stan można dostosować, przechodząc `RenderFragment`w niestandardowym pliku .</span><span class="sxs-lookup"><span data-stu-id="0576a-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="0576a-167">Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w `RemoteAuthenticatorView` następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="0576a-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="0576a-168">`Authentication`składnik *(Pages/Authentication.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="0576a-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="0576a-169">Ma `RemoteAuthenticatorView` jeden fragment, który może być używany dla trasy uwierzytelniania pokazano w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="0576a-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="0576a-170">Trasa</span><span class="sxs-lookup"><span data-stu-id="0576a-170">Route</span></span>                            | <span data-ttu-id="0576a-171">Fragment</span><span class="sxs-lookup"><span data-stu-id="0576a-171">Fragment</span></span>                |
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
