<span data-ttu-id="c5bdc-101">`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="c5bdc-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="c5bdc-102">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="c5bdc-102">For authenticated users:</span></span>
  * <span data-ttu-id="c5bdc-103">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c5bdc-103">Displays the current username.</span></span>
  * <span data-ttu-id="c5bdc-104">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c5bdc-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c5bdc-105">W przypadku użytkowników anonimowych program oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="c5bdc-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
