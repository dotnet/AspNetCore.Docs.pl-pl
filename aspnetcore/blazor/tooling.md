---
title: Narzędzia dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się więcej o narzędziach dostępnych do kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 500342ac979efdee824ac0d4b5757ca9804f3b30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054817"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>Narzędzia dla ASP.NET Core Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .

1. Tworzenie nowego projektu.

1. Wybierz pozycję **Blazor aplikacja** . Wybierz opcję **Dalej** .

1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz pozycję **Utwórz** .

1. Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** . Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** . Wybierz pozycję **Utwórz** .

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .

1. Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.

Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end

::: zone pivot="linux"

1. Zainstaluj najnowszą wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download). Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet --version
   ```

1. Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com).

1. Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .

1. Otwórz `WebApplication1` folder w Visual Studio Code.

1. Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu. Wybierz pozycję **Tak** .

1. Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.

## <a name="trust-a-development-certificate"></a>Ufanie certyfikatowi Deweloperskiemu

Nie istnieje scentralizowany sposób zaufania certyfikatu w systemie Linux. Zazwyczaj przyjmuje się jedną z następujących metod:

* Wyklucz adres URL aplikacji na liście wykluczeń przeglądarki.
* Ufaj wszystkim certyfikatom z podpisem własnym dla programu `localhost` .
* Dodaj certyfikat do listy zaufanych certyfikatów w przeglądarce.

Aby uzyskać więcej informacji, zobacz wskazówki dostarczone przez producenta przeglądarki i dystrybucję systemu Linux.

::: zone-end

::: zone pivot="macos"

1. Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania** .

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App** .

   Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** . Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** . Wybierz opcję **Dalej** .

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .

1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** . Wybierz opcję **Dalej** .

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` . Wybierz pozycję **Utwórz** .

1. Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera* . Uruchom **aplikację z**  >  przyciskiem Uruchom **debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem* .

Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu. Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end
