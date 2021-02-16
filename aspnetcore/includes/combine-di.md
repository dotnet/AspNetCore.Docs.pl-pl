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

Należy wziąć pod uwagę następującą `ConfigureServices` metodę, która rejestruje usługi i konfiguruje opcje:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Powiązane grupy rejestracji można przenieść do metody rozszerzenia w celu zarejestrowania usług. Na przykład usługi konfiguracji są dodawane do następującej klasy:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Pozostałe usługi są zarejestrowane w podobnej klasie. W poniższej `ConfigureServices` metodzie zarejestrowano usługi przy użyciu nowych metod rozszerzających:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Uwaga:_** Każda `services.Add{GROUP_NAME}` Metoda rozszerzania dodaje i potencjalnie konfiguruje usługi. Na przykład <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> dodaje kontrolery MVC usług z wymaganymi widokami i <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> dodaje do nich Razor strony usług. Zalecamy, aby aplikacje były zgodne z tą konwencją nazewnictwa. Umieść metody rozszerzające w <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> przestrzeni nazw, aby hermetyzować grupy rejestracji usług.
