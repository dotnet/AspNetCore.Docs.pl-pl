Składnik (*App. Razor*) jest podobny do `App` składnika znalezionego w aplikacjach serwera Blazor: `App`

* `CascadingAuthenticationState` Składnik zarządza uwidacznianiem `AuthenticationState` w pozostałej części aplikacji.
* `AuthorizeRouteView` Składnik gwarantuje, że bieżący użytkownik ma uprawnienia dostępu do danej strony lub w inny sposób renderuje `RedirectToLogin` składnik.
* `RedirectToLogin` Składnik zarządza przekierowaniem nieautoryzowanych użytkowników na stronę logowania.

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
