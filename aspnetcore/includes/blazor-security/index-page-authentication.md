<span data-ttu-id="1be73-101">Strona indeks ( `wwwroot/index.html` ) zawiera skrypt, który definiuje `AuthenticationService` w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1be73-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="1be73-102">`AuthenticationService` obsługuje szczegóły niskiego poziomu protokołu OIDC.</span><span class="sxs-lookup"><span data-stu-id="1be73-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="1be73-103">Aplikacja wewnętrznie wywołuje metody zdefiniowane w skrypcie w celu wykonywania operacji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1be73-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
