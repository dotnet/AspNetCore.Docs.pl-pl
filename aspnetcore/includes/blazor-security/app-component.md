<span data-ttu-id="38867-101">Składnik (*App. Razor*) jest podobny do `App` składnika znalezionego w aplikacjach serwera Blazor: `App`</span><span class="sxs-lookup"><span data-stu-id="38867-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="38867-102">`CascadingAuthenticationState` Składnik zarządza uwidacznianiem `AuthenticationState` w pozostałej części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="38867-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="38867-103">`AuthorizeRouteView` Składnik gwarantuje, że bieżący użytkownik ma uprawnienia dostępu do danej strony lub w inny sposób renderuje `RedirectToLogin` składnik.</span><span class="sxs-lookup"><span data-stu-id="38867-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="38867-104">`RedirectToLogin` Składnik zarządza przekierowaniem nieautoryzowanych użytkowników na stronę logowania.</span><span class="sxs-lookup"><span data-stu-id="38867-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
