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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551500"
---
Przyczepy HTTP są podobne do nagłówków HTTP, z tą różnicą, że są wysyłane po wysłaniu treści odpowiedzi. W przypadku usług IIS i HTTP.sys obsługiwane są tylko przyczepy z odpowiedzią HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

W poprzednim przykładowym kodzie:

* `SupportsTrailers` zapewnia, że przyczepy są obsługiwane na potrzeby odpowiedzi.
* `DeclareTrailer` Dodaje nazwę danej przyczepy do `Trailer` nagłówka odpowiedzi. Deklarowanie przyczep z odpowiedzią jest opcjonalne, ale zalecane. Jeśli `DeclareTrailer` jest wywoływana, musi być przed wysłaniem nagłówków odpowiedzi.
* `AppendTrailer` dołącza przyczepę.
