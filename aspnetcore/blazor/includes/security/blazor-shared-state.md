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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552870"
---
<span data-ttu-id="5738c-101">Blazor aplikacje serwera na żywo w pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="5738c-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="5738c-102">Oznacza to, że istnieje wiele aplikacji hostowanych w ramach tego samego procesu.</span><span class="sxs-lookup"><span data-stu-id="5738c-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="5738c-103">Dla każdej sesji aplikacji Blazor uruchamia obwód z własnym zakresem di kontenera.</span><span class="sxs-lookup"><span data-stu-id="5738c-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="5738c-104">Oznacza to, że usługi o określonym zakresie są unikatowe dla każdej Blazor sesji.</span><span class="sxs-lookup"><span data-stu-id="5738c-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="5738c-105">Nie zalecamy stosowania aplikacji w tym samym stanie udostępniania serwera przy użyciu pojedynczych usług, chyba że jest to wyjątkowo ważne, ponieważ może to spowodować powstanie luk w zabezpieczeniach, takich jak przeciek stanu użytkownika w obwodach.</span><span class="sxs-lookup"><span data-stu-id="5738c-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="5738c-106">W aplikacjach można używać stanowych pojedynczych usług, Blazor Jeśli są one przeznaczone specjalnie do tego celu.</span><span class="sxs-lookup"><span data-stu-id="5738c-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="5738c-107">Na przykład można użyć pamięci podręcznej pamięci jako pojedynczej, ponieważ wymaga ona klucza w celu uzyskania dostępu do danego wpisu, przy założeniu, że użytkownicy nie mają kontroli nad używanymi kluczami pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="5738c-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="5738c-108">**Ponadto ze względów bezpieczeństwa nie można używać <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> w Blazor aplikacjach.**</span><span class="sxs-lookup"><span data-stu-id="5738c-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="5738c-109">Blazor aplikacje działają poza kontekstem potoku ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5738c-109">Blazor apps run outside of the context of the ASP.NET Core pipeline.</span></span> <span data-ttu-id="5738c-110"><xref:Microsoft.AspNetCore.Http.HttpContext>Nie ma gwarancji, że jest ona dostępna w ramach programu <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , ani nie gwarantuje posiadania kontekstu, w którym uruchomiono Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="5738c-110">The <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor is it guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="5738c-111">Zalecanym sposobem przekazywania stanu żądania do Blazor aplikacji jest użycie parametrów do składnika głównego podczas początkowego renderowania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5738c-111">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="5738c-112">Zdefiniuj klasę zawierającą wszystkie dane, które chcesz przekazać do Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5738c-112">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="5738c-113">Wypełnij te dane ze Razor strony przy użyciu <xref:Microsoft.AspNetCore.Http.HttpContext> dostępnych w tym czasie.</span><span class="sxs-lookup"><span data-stu-id="5738c-113">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="5738c-114">Przekaż dane do Blazor aplikacji jako parametr do głównego składnika (aplikacji).</span><span class="sxs-lookup"><span data-stu-id="5738c-114">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="5738c-115">Zdefiniuj parametr w składniku głównym, aby przechowywać dane przesyłane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5738c-115">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="5738c-116">Korzystanie z danych specyficznych dla użytkownika w aplikacji; lub alternatywnie Skopiuj te dane do usługi w zakresie w programie, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Aby można było jej używać w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5738c-116">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="5738c-117">Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .</span><span class="sxs-lookup"><span data-stu-id="5738c-117">For more information and example code, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>
