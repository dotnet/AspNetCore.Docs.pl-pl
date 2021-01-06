`RedirectToLogin`Składnik ( `Shared/RedirectToLogin.razor` ):

* Zarządza przekierowywaniem nieautoryzowanych użytkowników do strony logowania.
* Zachowuje bieżący adres URL, do którego użytkownik próbuje uzyskać dostęp, aby można go było zwrócić do tej strony w przypadku pomyślnego uwierzytelnienia.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
