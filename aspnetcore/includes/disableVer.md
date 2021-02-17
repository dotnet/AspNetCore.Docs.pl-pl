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
### <a name="disable-default-account-verification"></a><span data-ttu-id="2ac5c-101">Wyłącz weryfikację konta domyślnego</span><span class="sxs-lookup"><span data-stu-id="2ac5c-101">Disable default account verification</span></span>

<span data-ttu-id="2ac5c-102">W przypadku szablonów domyślnych użytkownik zostanie przekierowany do lokalizacji, w `Account.RegisterConfirmation` której można wybrać link, aby potwierdzić to konto.</span><span class="sxs-lookup"><span data-stu-id="2ac5c-102">With the default templates, the user is redirected to the `Account.RegisterConfirmation` where they can select a link to have the account confirmed.</span></span> <span data-ttu-id="2ac5c-103">Wartość domyślna `Account.RegisterConfirmation` jest używana ***tylko*** do testowania, dlatego Automatyczna weryfikacja konta powinna być wyłączona w aplikacji produkcyjnej.</span><span class="sxs-lookup"><span data-stu-id="2ac5c-103">The default `Account.RegisterConfirmation` is used ***only*** for testing, automatic account verification should be disabled in a production app.</span></span>

<span data-ttu-id="2ac5c-104">Aby wymagać potwierdzonego konta i uniemożliwić natychmiastowe Logowanie przy rejestracji, ustaw `DisplayConfirmAccountLink = false` w */Areas/ Identity /Pages/Account/RegisterConfirmation.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ac5c-104">To require a confirmed account and prevent immediate login at registration, set `DisplayConfirmAccountLink = false` in */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*:</span></span>

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]