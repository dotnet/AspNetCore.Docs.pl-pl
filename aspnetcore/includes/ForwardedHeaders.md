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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552249"
---
<span data-ttu-id="2f273-101">Przekierowane nagłówki oprogramowanie pośredniczące powinno być uruchamiane przed innym programem pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="2f273-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="2f273-102">Takie porządkowanie zapewnia, że oprogramowanie pośredniczące polegające na informacjach o przekazanych nagłówkach może zużywać wartości nagłówka do przetworzenia.</span><span class="sxs-lookup"><span data-stu-id="2f273-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="2f273-103">Aby uruchomić przekazane nagłówki oprogramowania pośredniczącego po diagnostyki i obsłudze błędów oprogramowania pośredniczącego, zobacz kolejność wydawania w postaci [nagłówków dalej](xref:host-and-deploy/proxy-load-balancer#fhmo).</span><span class="sxs-lookup"><span data-stu-id="2f273-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>