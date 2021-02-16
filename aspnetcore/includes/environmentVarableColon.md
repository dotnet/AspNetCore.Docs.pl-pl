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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536311"
---
<span data-ttu-id="6a22b-101">`:`Separator nie działa ze zmiennymi kluczy hierarchicznych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="6a22b-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="6a22b-102">`__`, podwójne podkreślenie:</span><span class="sxs-lookup"><span data-stu-id="6a22b-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="6a22b-103">Obsługiwane przez wszystkie platformy.</span><span class="sxs-lookup"><span data-stu-id="6a22b-103">Supported by all platforms.</span></span> <span data-ttu-id="6a22b-104">Na przykład `:` separator nie jest obsługiwany przez [bash](https://linuxhint.com/bash-environment-variables/), ale `__` jest.</span><span class="sxs-lookup"><span data-stu-id="6a22b-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="6a22b-105">Automatycznie zastąpione przez `:`</span><span class="sxs-lookup"><span data-stu-id="6a22b-105">Automatically replaced by a `:`</span></span>