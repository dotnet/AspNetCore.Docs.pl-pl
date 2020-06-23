Strona indeks ( `wwwroot/index.html` ) zawiera skrypt, który definiuje `AuthenticationService` w języku JavaScript. `AuthenticationService`obsługuje szczegóły niskiego poziomu protokołu OIDC. Aplikacja wewnętrznie wywołuje metody zdefiniowane w skrypcie w celu wykonywania operacji uwierzytelniania.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
