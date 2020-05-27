Strona utworzona przez `Authentication` składnik (*Pages/Authentication. Razor*) definiuje trasy wymagane do obsługi różnych etapów uwierzytelniania.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Składnik:

* Jest dostarczany przez pakiet [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
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
