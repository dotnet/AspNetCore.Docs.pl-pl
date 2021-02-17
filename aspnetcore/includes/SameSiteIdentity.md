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
ASP.NET Core [Identity](xref:security/authentication/identity) jest w dużym stopniu niezależny od [SameSite cookie s](xref:security/samesite) z wyjątkiem zaawansowanych scenariuszy, takich jak `IFrames` lub `OpenIdConnect` integracji.

W przypadku korzystania z programu nie należy `Identity` dodawać  żadnych cookie dostawców ani wywoływać ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` .