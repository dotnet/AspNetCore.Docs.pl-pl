---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij korzystać z Blazor programu, tworząc Blazor aplikację przy użyciu wybranego przez siebie narzędzia.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111074"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Wprowadzenie do ASP.NET Core Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aby rozpocząć pracę z usługą Blazor, postępuj zgodnie ze wskazówkami dotyczącymi wybranych narzędzi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Aby utworzyć aplikacje serwera Blazor, zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) przy użyciu obciążeń **ASP.NET i Web Development** .

   Aby utworzyć Blazor Server i Blazor aplikacje webassembly, zainstaluj najnowszą wersję zapoznawczą programu [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) przy użyciu obciążenia **ASP.NET i sieci Web** .

   Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor webassembly* i *Blazor Server*, <xref:blazor/hosting-models>Zobacz.

1. Zainstaluj szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Tworzenie nowego projektu.

1. Wybierz pozycję **aplikacja Blazor**. Wybierz opcję **Dalej**.

1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz pozycję **Utwórz**.

1. W przypadku środowiska webassembly Blazor (Visual Studio 16,6 Preview 2 lub nowszego) wybierz szablon **aplikacji Blazor webassembly** . W przypadku środowiska serwera Blazor (Visual Studio 16,4 lub nowszego) wybierz szablon **aplikacji Blazor Server** . Wybierz pozycję **Utwórz**.

1. Naciśnij klawisz <kbd>Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Zainstaluj [zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcjonalnie można zainstalować szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > [Zestaw .NET Core SDK w wersji 3.1.201 lub nowszej](https://dotnet.microsoft.com/download/dotnet-core/3.1) jest **wymagana** , aby można było użyć szablonu webassembly 3,2 Blazor. Potwierdź zainstalowaną zestaw .NET Core SDK wersję, `dotnet --version` uruchamiając polecenie w powłoce poleceń programu.

1. Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

1. Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym `true`na.

1. W przypadku środowiska serwera Blazor wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   W przypadku środowiska webassembly Blazor wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor Server* i *Blazor webassembly*, <xref:blazor/hosting-models>Zobacz.

1. Otwórz folder *WebApplication1* w Visual Studio Code.

1. Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu. Wybierz pozycję **tak**.

1. Na serwerze Blazor Uruchom aplikację przy użyciu debugera Visual Studio Code.

   W programie Blazor webassembly Uruchom aplikację przy użyciu konfiguracji uruchamiania programu **.NET Core (Blazor Standalone)** , a następnie uruchom przeglądarkę przy użyciu **zestawu .NET Core Debug Blazor Web Assembly** (wymaga programu Chrome). Aby uzyskać więcej informacji, zobacz <xref:blazor/debug#visual-studio-code>.

1. W przeglądarce przejdź do `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Serwer Blazor jest obsługiwany w Visual Studio dla komputerów Mac. Zestaw webassembly Blazor nie jest obecnie obsługiwany. Aby kompilować Blazor aplikacje webassembly na macOS, postępuj zgodnie ze wskazówkami na karcie **interfejs wiersza polecenia platformy .NET Core** .

1. Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wybierz pozycję **plik** > **nowe rozwiązanie** lub Utwórz **Nowy projekt**.

1. Na pasku bocznym wybierz pozycję**aplikacja** **.NET Core** > .

1. Wybierz szablon **aplikacji Blazor Server** . Wybierz pozycję **Utwórz**.

   Aby uzyskać informacje na temat modelu hostingu serwera Blazor, <xref:blazor/hosting-models>Zobacz.

1. Ustaw platformę **docelową** na **platformę .NET Core 3,1** i wybierz pozycję **dalej**.

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1`. Wybierz pozycję **Utwórz**.

1. Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*. Uruchom aplikację przy użyciu **Rozpocznij debugowanie** , aby uruchomić aplikację *za pomocą debugera*.

Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

1. Zainstaluj [zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcjonalnie można zainstalować szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > [Zestaw .NET Core SDK w wersji 3.1.201 lub nowszej](https://dotnet.microsoft.com/download/dotnet-core/3.1) jest **wymagana** , aby można było użyć szablonu webassembly 3,2 Blazor. Potwierdź zainstalowaną zestaw .NET Core SDK wersję, `dotnet --version` uruchamiając polecenie w powłoce poleceń programu.

1. W przypadku środowiska serwera Blazor należy wykonać następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   W przypadku środowiska webassembly Blazor wykonaj następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor Server* i *Blazor webassembly*, <xref:blazor/hosting-models>Zobacz.

1. W przeglądarce przejdź do `https://localhost:5001`.

---

Na pasku bocznym są dostępne wiele stron:

* Strona główna
* Licznik
* Pobieranie danych

Na stronie licznik wybierz przycisk **kliknij** , aby zwiększyć licznik bez odświeżania strony. Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript, ale przy Blazor użyciu języka C#.

*Pages/Counter. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Żądanie `/counter` w przeglądarce, zgodnie z definicją w `@page` dyrektywie u góry, powoduje, że `Counter` składnik renderuje jego zawartość. Składniki są renderowane w postaci reprezentacji drzewa renderowania, która może być następnie używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.

Za każdym razem, gdy zostanie wybrany przycisk **kliknij mnie** :

* `onclick` Zdarzenie jest wyzwalane.
* `IncrementCount` Metoda jest wywoływana.
* Wartość `currentCount` jest zwiększana.
* Składnik jest ponownie renderowany.

Środowisko uruchomieniowe porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do Document Object Model (DOM).

Dodaj składnik do innego składnika przy użyciu składni języka HTML. Na przykład Dodaj `Counter` składnik do strony głównej aplikacji przez dodanie `<Counter />` elementu do `Index` składnika.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Uruchom aplikację. Strona główna ma swój własny licznik dostarczony przez `Counter` składnik.

Parametry składnika są określone przy użyciu atrybutów lub [zawartości podrzędnej](xref:blazor/components#child-content), które umożliwiają ustawianie właściwości składnika podrzędnego. Aby dodać parametr do `Counter` składnika, zaktualizuj `@code` blok składnika:

* Dodaj właściwość publiczną dla `IncrementAmount` `[Parameter]` atrybutu.
* Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` podczas zwiększania wartości. `currentCount`

*Pages/Counter. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Określ `<Counter>` element `IncrementAmount` w elemencie `Index` składnika przy użyciu atrybutu.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Uruchom aplikację. `Index` Składnik ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy jest zaznaczony przycisk **kliknij mnie** . Składnik (*Counter. Razor*) w `/counter` dalszym ciągu zwiększa się o jeden. `Counter`

## <a name="next-steps"></a>Następne kroki

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/templates>
* <xref:signalr/introduction>
