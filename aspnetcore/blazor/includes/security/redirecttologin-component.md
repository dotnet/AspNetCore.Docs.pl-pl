<span data-ttu-id="3ddf3-101">`RedirectToLogin`Składnik ( `Shared/RedirectToLogin.razor` ):</span><span class="sxs-lookup"><span data-stu-id="3ddf3-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="3ddf3-102">Zarządza przekierowywaniem nieautoryzowanych użytkowników do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="3ddf3-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="3ddf3-103">Zachowuje bieżący adres URL, do którego użytkownik próbuje uzyskać dostęp, aby można go było zwrócić do tej strony w przypadku pomyślnego uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="3ddf3-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
