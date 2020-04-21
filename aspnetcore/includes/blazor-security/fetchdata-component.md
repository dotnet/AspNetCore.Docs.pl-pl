Składnik `FetchData` pokazuje, jak:

* Aprowizuj token dostępu.
* Użyj tokenu dostępu, aby wywołać chroniony interfejs API zasobów w aplikacji *Serwer.*

Dyrektywa `@attribute [Authorize]` wskazuje system autoryzacji Blazor WebAssembly, że użytkownik musi być autoryzowany, aby odwiedzić ten składnik. Obecność atrybutu w aplikacji *klienta* nie uniemożliwia wywoływania interfejsu API na serwerze bez odpowiednich poświadczeń. Aplikacja *Server* również `[Authorize]` należy użyć w odpowiednich punktach końcowych, aby poprawnie je chronić.

`AuthenticationService.RequestAccessToken();`zajmuje się żądanie tokenu dostępu, który można dodać do żądania wywołania interfejsu API. Jeśli token jest buforowany lub usługa jest w stanie aprowizować nowy token dostępu bez interakcji z użytkownikiem, żądanie tokenu powiedzie się. W przeciwnym razie żądanie tokenu kończy się niepowodzeniem.

Aby uzyskać rzeczywisty token do uwzględnienia w żądaniu, aplikacja musi sprawdzić, czy żądanie powiodło się, wywołując `tokenResult.TryGetToken(out var token)`. 

Jeśli żądanie zakończyło się pomyślnie, zmienna tokenu jest wypełniona tokenem dostępu. Właściwość `Value` tokenu udostępnia ciąg literału do `Authorization` uwzględnienia w nagłówku żądania.

Jeśli żądanie nie powiodło się, ponieważ token nie może być aprowizowana bez interakcji z użytkownikiem, wynik tokenu zawiera adres URL przekierowania. Przejście do tego adresu URL powoduje przejście użytkownika do strony logowania i powrót do bieżącej strony po pomyślnym uwierzytelnieniu.

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

Aby uzyskać więcej informacji, zobacz [Zapisywanie stanu aplikacji przed operacją uwierzytelniania](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
