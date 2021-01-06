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
ms.openlocfilehash: 4813668f5278473fbaae36d572e69700b3fe771a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764240"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>Narzędzia dla ASP.NET Core Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .

1. Tworzenie nowego projektu.

1. Wybierz pozycję **Blazor aplikacja**. Wybierz pozycję **Dalej**.

1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** . Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** . Wybierz przycisk **Utwórz**.

   W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .

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

   W przypadku środowiska hostowanego Blazor WebAssembly Dodaj opcję hostowaną ( `-ho` lub `--hosted` ) do polecenia:
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .

1. Otwórz `WebApplication1` folder w Visual Studio Code.

1. Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu. Wybierz pozycję **Tak**.

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

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .

   Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** . Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** . Wybierz pozycję **Dalej**.

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .

1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**. Wybierz pozycję **Dalej**.

1. W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` . Wybierz przycisk **Utwórz**.

1. Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*. Uruchom **aplikację z**  >  przyciskiem Uruchom **debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.

Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu. Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a>Używanie Visual Studio Code do tworzenia aplikacji dla wielu platform Blazor

[Visual Studio Code](https://code.visualstudio.com/) to środowisko programistyczne (IDE) platformy Open Source, które może służyć do tworzenia Blazor aplikacji. Użyj interfejsu wiersza polecenia platformy .NET, aby utworzyć nową Blazor aplikację do tworzenia przy użyciu Visual Studio Code. Aby uzyskać więcej informacji, zobacz [wersję systemu Linux w tym artykule](/aspnet/core/blazor/tooling?pivots=linux).

## <a name="no-locblazor-template-options"></a>Blazor Opcje szablonu

BlazorStruktura zawiera szablony służące do tworzenia nowych aplikacji dla każdego z dwóch Blazor modeli hostingu. Szablony są używane do tworzenia nowych Blazor projektów i rozwiązań, niezależnie od narzędzi wybranych do Blazor programowania (Visual Studio, Visual Studio dla komputerów Mac, Visual Studio Code lub interfejsu wiersza polecenia platformy .NET):

* Blazor WebAssembly szablon projektu: `blazorwasm`
* Blazor Server szablon projektu: `blazorserver`

Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .

Opcje szablonu są dostępne przez przekazanie opcji pomocy ( `-h` lub `--help` ) do [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia CLI w powłoce poleceń:

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
