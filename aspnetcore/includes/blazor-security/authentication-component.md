<span data-ttu-id="84fdb-101">Strona utworzona przez `Authentication` składnik (*Pages/Authentication. Razor*) definiuje trasy wymagane do obsługi różnych etapów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="84fdb-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="84fdb-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Składnik:</span><span class="sxs-lookup"><span data-stu-id="84fdb-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="84fdb-103">Jest dostarczany przez pakiet [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="84fdb-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="84fdb-104">Zarządza wykonywaniem odpowiednich czynności na każdym etapie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="84fdb-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
