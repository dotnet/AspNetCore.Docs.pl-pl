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
ms.openlocfilehash: b2519eaab7a83d66e60be05b1c2deeb8094b016f
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552454"
---
Strona indeks ( `wwwroot/index.html` ) zawiera skrypt, który definiuje `AuthenticationService` w języku JavaScript. `AuthenticationService` obsługuje szczegóły niskiego poziomu protokołu OIDC. Aplikacja wewnętrznie wywołuje metody zdefiniowane w skrypcie w celu wykonywania operacji uwierzytelniania.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
