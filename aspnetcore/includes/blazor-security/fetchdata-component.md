Składnik `FetchData` pokazuje, jak:

* Inicjowanie obsługi administracyjnej tokenu dostępu.
* Użyj tokenu dostępu, aby wywołać interfejs API zasobów chronionych w aplikacji *serwera* .

`@attribute [Authorize]` Dyrektywa wskazuje system autoryzacji zestawu webassembly Blazor, który użytkownik musi mieć autoryzację, aby móc odwiedzić ten składnik. Obecność atrybutu w aplikacji *klienckiej* nie zapobiega WYWOŁYWANIU interfejsu API na serwerze bez poprawnych poświadczeń. Aplikacja *serwera* musi również użyć `[Authorize]` odpowiednich punktów końcowych, aby je poprawnie chronić.

`IAccessTokenProvider.RequestAccessToken();`wykonuje żądanie tokenu dostępu, który można dodać do żądania, aby wywołać interfejs API. Jeśli token jest buforowany lub usługa jest w stanie zainicjować nowy token dostępu bez interakcji z użytkownikiem, żądanie tokenu powiedzie się. W przeciwnym razie żądanie tokenu kończy się niepowodzeniem z `AccessTokenNotAvailableException` błędem, który jest `try-catch` przechwytywany w instrukcji.

Aby uzyskać rzeczywisty token do uwzględnienia w żądaniu, aplikacja musi sprawdzić, czy żądanie powiodło się, wywołując `tokenResult.TryGetToken(out var token)`metodę. 

Jeśli żądanie zakończyło się pomyślnie, zmienna tokenu jest wypełniana tokenem dostępu. `Value` Właściwość tokenu ujawnia ciąg literału, który ma zostać uwzględniony w `Authorization` nagłówku żądania.

Jeśli żądanie nie powiodło się, ponieważ nie można zainicjować obsługi administracyjnej tokenu bez interakcji z użytkownikiem, wynik tokenu zawiera adres URL przekierowania. Przechodzenie do tego adresu URL powoduje, że użytkownik jest zalogowany na stronie logowania i z powrotem do bieżącej strony po pomyślnym uwierzytelnieniu.

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
