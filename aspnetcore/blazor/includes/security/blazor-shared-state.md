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
Blazor aplikacje serwera na żywo w pamięci serwera. Oznacza to, że istnieje wiele aplikacji hostowanych w ramach tego samego procesu. Dla każdej sesji aplikacji Blazor uruchamia obwód z własnym zakresem di kontenera. Oznacza to, że usługi o określonym zakresie są unikatowe dla każdej Blazor sesji.

> [!WARNING]
> Nie zalecamy stosowania aplikacji w tym samym stanie udostępniania serwera przy użyciu pojedynczych usług, chyba że jest to wyjątkowo ważne, ponieważ może to spowodować powstanie luk w zabezpieczeniach, takich jak przeciek stanu użytkownika w obwodach.

W aplikacjach można używać stanowych pojedynczych usług, Blazor Jeśli są one przeznaczone specjalnie do tego celu. Na przykład można użyć pamięci podręcznej pamięci jako pojedynczej, ponieważ wymaga ona klucza w celu uzyskania dostępu do danego wpisu, przy założeniu, że użytkownicy nie mają kontroli nad używanymi kluczami pamięci podręcznej.

**Ponadto ze względów bezpieczeństwa nie można używać <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> w Blazor aplikacjach.** Blazor aplikacje działają poza kontekstem potoku ASP.NET Core. <xref:Microsoft.AspNetCore.Http.HttpContext>Nie ma gwarancji, że jest ona dostępna w ramach programu <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , ani nie gwarantuje posiadania kontekstu, w którym uruchomiono Blazor aplikację.

Zalecanym sposobem przekazywania stanu żądania do Blazor aplikacji jest użycie parametrów do składnika głównego podczas początkowego renderowania aplikacji:

* Zdefiniuj klasę zawierającą wszystkie dane, które chcesz przekazać do Blazor aplikacji.
* Wypełnij te dane ze Razor strony przy użyciu <xref:Microsoft.AspNetCore.Http.HttpContext> dostępnych w tym czasie.
* Przekaż dane do Blazor aplikacji jako parametr do głównego składnika (aplikacji).
* Zdefiniuj parametr w składniku głównym, aby przechowywać dane przesyłane do aplikacji.
* Korzystanie z danych specyficznych dla użytkownika w aplikacji; lub alternatywnie Skopiuj te dane do usługi w zakresie w programie, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Aby można było jej używać w całej aplikacji.

Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .
