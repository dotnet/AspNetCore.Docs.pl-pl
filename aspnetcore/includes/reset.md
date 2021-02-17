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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551824"
---
Wartość Resetuj umożliwia serwerowi zresetowanie żądania HTTP/2 z określonym kodem błędu. Żądanie resetowania jest uznawane za przerwane.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` w poprzednim przykładzie kodu określa `INTERNAL_ERROR` Kod błędu. Więcej informacji o kodach błędów protokołu HTTP/2 znajduje się w [sekcji kod błędu specyfikacji protokołu HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).