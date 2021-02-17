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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552203"
---
<span data-ttu-id="d85e7-101">ASP.NET Core [Identity](xref:security/authentication/identity) jest w dużym stopniu niezależny od [SameSite cookie s](xref:security/samesite) z wyjątkiem zaawansowanych scenariuszy, takich jak `IFrames` lub `OpenIdConnect` integracji.</span><span class="sxs-lookup"><span data-stu-id="d85e7-101">ASP.NET Core [Identity](xref:security/authentication/identity) is largely unaffected by [SameSite cookies](xref:security/samesite) except for advanced scenarios like `IFrames` or `OpenIdConnect` integration.</span></span>

<span data-ttu-id="d85e7-102">W przypadku korzystania z programu nie należy `Identity` dodawać  żadnych cookie dostawców ani wywoływać ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` .</span><span class="sxs-lookup"><span data-stu-id="d85e7-102">When using `Identity`, do ***not*** add any cookie providers or call ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)`, `Identity` takes care of that.</span></span>