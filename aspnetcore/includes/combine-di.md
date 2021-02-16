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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536318"
---
<a name="csc"></a>

<span data-ttu-id="164de-101">Należy wziąć pod uwagę następującą `ConfigureServices` metodę, która rejestruje usługi i konfiguruje opcje:</span><span class="sxs-lookup"><span data-stu-id="164de-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="164de-102">Powiązane grupy rejestracji można przenieść do metody rozszerzenia w celu zarejestrowania usług.</span><span class="sxs-lookup"><span data-stu-id="164de-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="164de-103">Na przykład usługi konfiguracji są dodawane do następującej klasy:</span><span class="sxs-lookup"><span data-stu-id="164de-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="164de-104">Pozostałe usługi są zarejestrowane w podobnej klasie.</span><span class="sxs-lookup"><span data-stu-id="164de-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="164de-105">W poniższej `ConfigureServices` metodzie zarejestrowano usługi przy użyciu nowych metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="164de-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="164de-106">**_Uwaga:_** Każda `services.Add{GROUP_NAME}` Metoda rozszerzania dodaje i potencjalnie konfiguruje usługi.</span><span class="sxs-lookup"><span data-stu-id="164de-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="164de-107">Na przykład <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> dodaje kontrolery MVC usług z wymaganymi widokami i <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> dodaje do nich Razor strony usług.</span><span class="sxs-lookup"><span data-stu-id="164de-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="164de-108">Zalecamy, aby aplikacje były zgodne z tą konwencją nazewnictwa.</span><span class="sxs-lookup"><span data-stu-id="164de-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="164de-109">Umieść metody rozszerzające w <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> przestrzeni nazw, aby hermetyzować grupy rejestracji usług.</span><span class="sxs-lookup"><span data-stu-id="164de-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
