<span data-ttu-id="f4a02-101">`App`Składnik ( `App.razor` ) jest podobny do `App` składnika znalezionego w aplikacjach serwera Blazor:</span><span class="sxs-lookup"><span data-stu-id="f4a02-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="f4a02-102"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>Składnik zarządza uwidacznianiem w <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> pozostałej części aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f4a02-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="f4a02-103"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>Składnik gwarantuje, że bieżący użytkownik ma uprawnienia dostępu do danej strony lub w inny sposób renderuje `RedirectToLogin` składnik.</span><span class="sxs-lookup"><span data-stu-id="f4a02-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="f4a02-104">`RedirectToLogin`Składnik zarządza przekierowaniem nieautoryzowanych użytkowników na stronę logowania.</span><span class="sxs-lookup"><span data-stu-id="f4a02-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
