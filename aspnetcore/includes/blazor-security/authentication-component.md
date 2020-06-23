Strona utworzona przez `Authentication` składnik ( `Pages/Authentication.razor` ) definiuje trasy wymagane do obsługi różnych etapów uwierzytelniania.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Składnik:

* Jest dostarczany przez [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pakiet.
* Zarządza wykonywaniem odpowiednich czynności na każdym etapie uwierzytelniania.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
