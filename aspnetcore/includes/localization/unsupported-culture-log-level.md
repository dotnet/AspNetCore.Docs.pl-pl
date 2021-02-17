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
> Przed ASP.NET Core aplikacje sieci Web 3,0 zapisują jeden dziennik typu `LogLevel.Warning` na żądanie, jeśli żądana kultura nie jest obsługiwana. Rejestrowanie jednego `LogLevel.Warning` na żądanie może spowodować duże pliki dziennika z nadmiarowymi informacjami. To zachowanie zostało zmienione w ASP.NET 3,0. `RequestLocalizationMiddleware`Zapisuje dziennik typu `LogLevel.Debug` , co zmniejsza rozmiar dzienników produkcyjnych.
