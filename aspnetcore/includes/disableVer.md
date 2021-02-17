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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552159"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Wyłącz weryfikację konta domyślnego

W przypadku szablonów domyślnych użytkownik zostanie przekierowany do lokalizacji, w `Account.RegisterConfirmation` której można wybrać link, aby potwierdzić to konto. Wartość domyślna `Account.RegisterConfirmation` jest używana ***tylko*** do testowania, dlatego Automatyczna weryfikacja konta powinna być wyłączona w aplikacji produkcyjnej.

Aby wymagać potwierdzonego konta i uniemożliwić natychmiastowe Logowanie przy rejestracji, ustaw `DisplayConfirmAccountLink = false` w */Areas/ Identity /Pages/Account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]