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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551568"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="56c48-101">Udostępnianie kodu międzyoperacyjnego w bibliotece klas</span><span class="sxs-lookup"><span data-stu-id="56c48-101">Share interop code in a class library</span></span>

<span data-ttu-id="56c48-102">Kod międzyoperacyjny JS można uwzględnić w bibliotece klas, co umożliwia udostępnianie kodu w pakiecie NuGet.</span><span class="sxs-lookup"><span data-stu-id="56c48-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="56c48-103">Biblioteka klas obsługuje Osadzanie zasobów JavaScript w skompilowanym zestawie.</span><span class="sxs-lookup"><span data-stu-id="56c48-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="56c48-104">Pliki JavaScript są umieszczane w `wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="56c48-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="56c48-105">Narzędzia te zajmują się osadzaniem zasobów podczas kompilowania biblioteki.</span><span class="sxs-lookup"><span data-stu-id="56c48-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="56c48-106">Skompilowany pakiet NuGet jest przywoływany w pliku projektu aplikacji w taki sam sposób, w jaki jest przywoływany każdy pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="56c48-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="56c48-107">Po przywróceniu pakietu kod aplikacji może być wywoływany w języku JavaScript, tak jakby był w języku C#.</span><span class="sxs-lookup"><span data-stu-id="56c48-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="56c48-108">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="56c48-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
