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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly dodatkowe scenariusze zabezpieczeń

Przez [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Żądanie dodatkowych tokenów dostępu

Większość aplikacji wymaga tylko tokenu dostępu do interakcji z chronionymi zasobami, których używają. W niektórych scenariuszach aplikacja może wymagać więcej niż jednego tokenu w celu interakcji z dwoma lub więcej zasobów.

W poniższym przykładzie dodatkowe zakresy interfejsu API programu Microsoft Graph usługi Azure Active Directory (AAD) są wymagane przez aplikację do odczytywania danych użytkownika i wysyłania poczty. Po dodaniu uprawnień interfejsu API programu Microsoft Graph w portalu usługi Azure AAD dodatkowe zakresy są konfigurowane w aplikacji klienta (`Program.Main`, *Program.cs*):

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

Metoda `IAccessTokenProvider.RequestToken` zapewnia przeciążenie, które umożliwia aplikacji aprowizować token z danym zestawem zakresów, jak pokazano w poniższym przykładzie:

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

`TryGetToken`Zwraca:

* `true`z `token` do użytku.
* `false`jeśli token nie jest pobierany.

## <a name="handle-token-request-errors"></a>Obsługa błędów żądań tokenu

Gdy aplikacja jednostronicowa (SPA) uwierzytelnia użytkownika przy użyciu open id connect (OIDC), stan uwierzytelniania jest obsługiwany lokalnie w spa i w dostawcy tożsamości (IP) w postaci pliku cookie sesji, który jest ustawiany w wyniku podania przez użytkownika swoich poświadczeń.

Tokeny, które adres IP emituje dla użytkownika zazwyczaj są ważne przez krótki okres czasu, około jednej godziny normalnie, więc aplikacja kliencka musi regularnie pobierać nowe tokeny. W przeciwnym razie użytkownik zostanie wylogowany po wygaśnięciu przyznanych tokenów. W większości przypadków klienci OIDC są w stanie aprowizować nowe tokeny bez konieczności ponownego uwierzytelniania użytkownika za pomocą stanu uwierzytelniania lub "sesji", która jest przechowywana w adresie IP.

Istnieją przypadki, w których klient nie może uzyskać tokenu bez interakcji z użytkownikiem, na przykład, gdy z jakiegoś powodu użytkownik jawnie wylogowuje się z adresu IP. Ten scenariusz występuje, `https://login.microsoftonline.com` jeśli użytkownik odwiedza i wylogowuje. W tych scenariuszach aplikacja nie wie od razu, że użytkownik wylogował się. Każdy token, który przechowuje klient może nie być już prawidłowy. Ponadto klient nie jest w stanie aprowizować nowego tokenu bez interakcji z użytkownikiem po wygaśnięciu bieżącego tokenu.

Te scenariusze nie są specyficzne dla uwierzytelniania opartego na tokenie. Są one częścią charakteru OSO. Spa przy użyciu plików cookie również nie można wywołać interfejsu API serwera, jeśli plik cookie uwierzytelniania jest usuwany.

Gdy aplikacja wykonuje wywołania interfejsu API do chronionych zasobów, należy pamiętać o następujących czynnościach:

* Aby aprowizować nowy token dostępu do wywołania interfejsu API, użytkownik może być zobowiązany do ponownego uwierzytelnienia.
* Nawet jeśli klient ma token, który wydaje się być prawidłowy, wywołanie serwera może zakończyć się niepowodzeniem, ponieważ token został odwołany przez użytkownika.

Gdy aplikacja żąda tokenu, istnieją dwa możliwe wyniki:

* Żądanie zakończy się pomyślnie, a aplikacja ma prawidłowy token.
* Żądanie kończy się niepowodzeniem, a aplikacja musi ponownie uwierzytelnić użytkownika, aby uzyskać nowy token.

Gdy żądanie tokenu nie powiedzie się, należy zdecydować, czy chcesz zapisać dowolny bieżący stan przed wykonaniem przekierowania. Istnieje kilka podejść z rosnącym poziomem złożoności:

* Przechowuj bieżący stan strony w magazynie sesji. Podczas `OnInitializeAsync`, sprawdź, czy stan można przywrócić przed kontynuowaniem.
* Dodaj parametr ciągu zapytania i użyj go jako sposobu sygnalizowania aplikacji, że musi ponownie nawodnić wcześniej zapisany stan.
* Dodaj parametr ciągu zapytania z unikatowym identyfikatorem do przechowywania danych w magazynie sesji bez ryzyka kolizji z innymi elementami.

W poniższym przykładzie pokazano, jak:

* Zachowaj stan przed przekierowaniem do strony logowania.
* Odzyskaj poprzedni stan po uwierzytelnieniu przy użyciu parametru ciągu zapytania.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Zapisywanie stanu aplikacji przed operacją uwierzytelniania

Podczas operacji uwierzytelniania istnieją przypadki, w których chcesz zapisać stan aplikacji, zanim przeglądarka zostanie przekierowana do adresu IP. Może to być w przypadku, gdy używasz coś takiego jak kontener stanu i chcesz przywrócić stan po pomyślnym uwierzytelnieniu. Niestandardowego obiektu stanu uwierzytelniania można użyć, aby zachować stan specyficzny dla aplikacji lub odwołanie do niego i przywrócić ten stan po pomyślnym zakończeniu operacji uwierzytelniania.

`Authentication`składnik *(Pages/Authentication.brzytwa):*

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

Domyślnie biblioteka `Microsoft.AspNetCore.Components.WebAssembly.Authentication` używa tras wyświetlanych w poniższej tabeli do reprezentowania różnych stanów uwierzytelniania.

| Trasa                            | Przeznaczenie |
| -------------------------------- | ------- |
| `authentication/login`           | Wyzwala operację logowania. |
| `authentication/login-callback`  | Obsługuje wynik każdej operacji logowania. |
| `authentication/login-failed`    | Wyświetla komunikaty o błędach, gdy operacja logowania nie powiedzie się z jakiegoś powodu. |
| `authentication/logout`          | Wyzwala operację wylogowywania. |
| `authentication/logout-callback` | Obsługuje wynik operacji wylogowywania. |
| `authentication/logout-failed`   | Wyświetla komunikaty o błędach, gdy operacja wylogowywania nie powiedzie się z jakiegoś powodu. |
| `authentication/logged-out`      | Wskazuje, że użytkownik pomyślnie wylogował się. |
| `authentication/profile`         | Wyzwala operację, aby edytować profil użytkownika. |
| `authentication/register`        | Wyzwala operację, aby zarejestrować nowego użytkownika. |

Trasy pokazane w powyższej tabeli `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`można konfigurować za pomocą programu . Podczas ustawiania opcji, aby zapewnić trasy niestandardowe, upewnij się, że aplikacja ma trasę, która obsługuje każdą ścieżkę.

W poniższym przykładzie wszystkie ścieżki są `/security`poprzedzone programem .

`Authentication`składnik *(Pages/Authentication.brzytwa):*

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

Jeśli wymaganie wymaga zupełnie innych ścieżek, ustaw trasy zgodnie `RemoteAuthenticatorView` z opisem wcześniej i renderuj z jawnym parametrem akcji:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Jeśli zdecydujesz się to zrobić, możesz podzielić interfejs użytkownika na różne strony.

## <a name="customize-the-authentication-user-interface"></a>Dostosowywanie interfejsu użytkownika uwierzytelniania

`RemoteAuthenticatorView`zawiera domyślny zestaw elementów interfejsu użytkownika dla każdego stanu uwierzytelniania. Każdy stan można dostosować, przechodząc `RenderFragment`w niestandardowym pliku . Aby dostosować wyświetlany tekst podczas początkowego procesu logowania, można zmienić w `RemoteAuthenticatorView` następujący sposób.

`Authentication`składnik *(Pages/Authentication.brzytwa):*

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

Ma `RemoteAuthenticatorView` jeden fragment, który może być używany dla trasy uwierzytelniania pokazano w poniższej tabeli.

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
