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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551845"
---
> [!NOTE]
> <span data-ttu-id="52859-101">Przed ASP.NET Core aplikacje sieci Web 3,0 zapisują jeden dziennik typu `LogLevel.Warning` na żądanie, jeśli żądana kultura nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="52859-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="52859-102">Rejestrowanie jednego `LogLevel.Warning` na żądanie może spowodować duże pliki dziennika z nadmiarowymi informacjami.</span><span class="sxs-lookup"><span data-stu-id="52859-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="52859-103">To zachowanie zostało zmienione w ASP.NET 3,0.</span><span class="sxs-lookup"><span data-stu-id="52859-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="52859-104">`RequestLocalizationMiddleware`Zapisuje dziennik typu `LogLevel.Debug` , co zmniejsza rozmiar dzienników produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="52859-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
