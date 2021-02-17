---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552096"
---
`App`Składnik ( `App.razor` ) jest podobny do `App` składnika znalezionego w Blazor Server aplikacjach:

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>Składnik zarządza uwidacznianiem w <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> pozostałej części aplikacji.
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>Składnik gwarantuje, że bieżący użytkownik ma uprawnienia dostępu do danej strony lub w inny sposób renderuje `RedirectToLogin` składnik.
* `RedirectToLogin`Składnik zarządza przekierowaniem nieautoryzowanych użytkowników na stronę logowania.

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

[!include[](../prefer-exact-matches.md)]
