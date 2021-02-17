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
<span data-ttu-id="1fa9f-101">Przyczepy HTTP są podobne do nagłówków HTTP, z tą różnicą, że są wysyłane po wysłaniu treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="1fa9f-102">W przypadku usług IIS i HTTP.sys obsługiwane są tylko przyczepy z odpowiedzią HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="1fa9f-103">W poprzednim przykładowym kodzie:</span><span class="sxs-lookup"><span data-stu-id="1fa9f-103">In the preceding example code:</span></span>

* <span data-ttu-id="1fa9f-104">`SupportsTrailers` zapewnia, że przyczepy są obsługiwane na potrzeby odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="1fa9f-105">`DeclareTrailer` Dodaje nazwę danej przyczepy do `Trailer` nagłówka odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="1fa9f-106">Deklarowanie przyczep z odpowiedzią jest opcjonalne, ale zalecane.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="1fa9f-107">Jeśli `DeclareTrailer` jest wywoływana, musi być przed wysłaniem nagłówków odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="1fa9f-108">`AppendTrailer` dołącza przyczepę.</span><span class="sxs-lookup"><span data-stu-id="1fa9f-108">`AppendTrailer` appends the trailer.</span></span>
