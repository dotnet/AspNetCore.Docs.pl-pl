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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552874"
---
> [!WARNING]
> Ze względów bezpieczeństwa należy zadecydować, aby powiązać `GET` dane żądania z właściwościami modelu strony. Sprawdź dane wejściowe użytkownika przed mapowaniem go na właściwości. W `GET` przypadku scenariuszy, które opierają się na ciągach zapytania lub wartościach trasy, Metoda ta jest przydatna.
>
> Aby powiązać właściwość na `GET` żądania, należy ustawić [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) `SupportsGet` właściwość atrybutu na `true` :
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Aby uzyskać więcej informacji, zobacz [ASP.NET Core Community standup: bind on Get Discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
