---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij od Blazor tworzenia Blazor aplikacji z wybranym oprzyrządowania.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: e9e6eeeb1d29aa529e43d75f5d3951d2c4384d7e
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002915"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Zacznij cie z ASP.NET Core Blazor

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aby rozpocząć pracę z Blazorem, postępuj zgodnie ze wskazówkami dotyczącymi wyboru narzędzi:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Aby utworzyć aplikacje Blazor Server, zainstaluj najnowszą wersję [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z obciążeniem **ASP.NET i tworzenia sieci Web.**

   Aby utworzyć aplikacje Blazor Server i Blazor WebAssembly, zainstaluj najnowszą wersję zapoznawczą [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) z obciążeniem **ASP.NET i tworzenia stron internetowych.**

   Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor WebAssembly* i *Blazor Server*, zobacz <xref:blazor/hosting-models>.

1. Tworzenie nowego projektu.

1. Wybierz **aplikację Blazor**. Wybierz **pozycję Dalej**.

1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu. Upewnij się, że wpis **Lokalizacja** jest poprawny lub podaj lokalizację dla projektu. Wybierz **pozycję Utwórz**.

1. Aby uzyskać środowisko Blazor WebAssembly (Visual Studio 16.6 Preview 2 lub nowsze), wybierz szablon **aplikacji Blazor WebAssembly.** Aby uzyskać środowisko serwera Blazor (Visual Studio 16.4 lub nowsze), wybierz szablon **aplikacji Serwera Blazor.** Wybierz **pozycję Utwórz**.

1. Naciśnij <kbd>klawisz Ctrl</kbd>+<kbd>F5,</kbd> aby uruchomić aplikację.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Zainstaluj pakiet [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcjonalnie zainstaluj szablon podglądu [Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > Do korzystania z szablonu 3.2 Preview 3 Blazor WebAssembly **wymagany** jest [plik .NET Core SDK w wersji 3.1.201 lub](https://dotnet.microsoft.com/download/dotnet-core/3.1) nowszej. Potwierdź zainstalowaną wersję .NET `dotnet --version` Core SDK, uruchamiając go w powłoce poleceń.

1. Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

1. Zainstaluj najnowsze [rozszerzenie kodu programu Visual Studio w języku C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i `debug.javascript.usePreview` rozszerzenie `true` [debugera JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z ustawieniem .

1. Aby uzyskać środowisko serwera Blazor, wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Aby uzyskać środowisko Blazor WebAssembly, wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor Server* i *Blazor WebAssembly*, zobacz <xref:blazor/hosting-models>.

1. Otwórz folder *WebApplication1* w programie Visual Studio Code.

1. IDE żąda, aby dodać zasoby do tworzenia i debugowania projektu. Wybierz **pozycję Tak**.

1. Za pomocą serwera Blazor Uruchom aplikację przy użyciu debugera kodu programu Visual Studio.

   Z Blazor WebAssembly, uruchom aplikację za pomocą **.NET Core Launch (Blazor Standalone)** uruchom konfigurację, a następnie uruchom przeglądarkę za pomocą **.NET Core Debug Blazor Web Assembly w** konfiguracji uruchamiania Chrome (wymaga Chrome). Aby uzyskać więcej informacji, zobacz <xref:blazor/debug#visual-studio-code>.

1. W przeglądarce przejdź `https://localhost:5001`do pliku .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Blazor Server jest obsługiwany w programie Visual Studio dla komputerów Mac. Blazor WebAssembly nie jest obecnie obsługiwany. Aby utworzyć aplikacje Blazor WebAssembly w systemie macOS, postępuj zgodnie ze wskazówkami na karcie **.NET Core CLI.**

1. Zainstaluj [program Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wybierz **pozycję Plik** > **nowego rozwiązania** lub utwórz nowy **projekt**.

1. Na pasku bocznym wybierz pozycję **.NET Core** > **App**.

1. Wybierz szablon **aplikacji Serwera Blazor.** Wybierz **pozycję Utwórz**.

   Aby uzyskać informacje na temat modelu <xref:blazor/hosting-models>hostingu Serwera Blazor, zobacz .

1. Ustaw **platformę docelową** na **.NET Core 3.1** i wybierz **pozycję Dalej**.

1. W polu **Nazwa projektu** nazwij aplikację `WebApplication1`. Wybierz **pozycję Utwórz**.

1. Wybierz **uruchom** > **uruchom bez debugowania,** aby uruchomić aplikację *bez debugera*. Uruchom aplikację za pomocą **debugowania startowego,** aby uruchomić aplikację *za pomocą debugera*.

Jeśli pojawia się monit, aby ufać certyfikatowi dewelopera, zaufaj certyfikatowi i kontynuuj.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

1. Zainstaluj pakiet [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Opcjonalnie zainstaluj szablon podglądu [Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > Do korzystania z szablonu 3.2 Preview 3 Blazor WebAssembly **wymagany** jest [plik .NET Core SDK w wersji 3.1.201 lub](https://dotnet.microsoft.com/download/dotnet-core/3.1) nowszej. Potwierdź zainstalowaną wersję .NET `dotnet --version` Core SDK, uruchamiając go w powłoce poleceń.

1. Aby uzyskać środowisko serwera Blazor, wykonaj następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Aby uzyskać środowisko Blazor WebAssembly, wykonaj następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor Server* i *Blazor WebAssembly*, zobacz <xref:blazor/hosting-models>.

1. W przeglądarce przejdź `https://localhost:5001`do pliku .

---

Wiele stron jest dostępnych na kartach na pasku bocznym:

* Strona główna
* Licznik
* Pobieranie danych

Na stronie Licznik wybierz przycisk **Kliknij mnie,** aby zwiększać licznik bez odświeżania strony. Zwiększanie licznika na stronie sieci Web zwykle wymaga pisania Blazor javascript, ale z można użyć języka C#.

*Strony/Counter.brzytwa*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Żądanie `/counter` w przeglądarce, zgodnie z `@page` dyrektywą u góry, powoduje, że `Counter` składnik do renderowania jego zawartości. Składniki renderowania w pamięci reprezentacji drzewa renderowania, które następnie mogą służyć do aktualizacji interfejsu użytkownika w sposób elastyczny i wydajny.

Za każdym razem, gdy wybrany jest przycisk **Kliknij mnie:**

* Zdarzenie `onclick` jest uruchamiane.
* Metoda `IncrementCount` jest wywoływana.
* Jest `currentCount` przyrostowany.
* Składnik jest renderowany ponownie.

Środowisko wykonawcze porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do modelu obiektu dokumentu (DOM).

Dodaj składnik do innego składnika przy użyciu składni HTML. Na przykład dodaj `Counter` składnik do strony głównej aplikacji, `<Counter />` dodając `Index` element do składnika.

*Strony/Index.brzytwa*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Uruchom aplikację. Strona główna ma swój własny `Counter` licznik dostarczony przez składnik.

Parametry komponentu są określane przy użyciu atrybutów lub [zawartości podrzędnej,](xref:blazor/components#child-content)które umożliwiają ustawienie właściwości składnika podrzędnego. Aby dodać parametr `Counter` do składnika, zaktualizuj blok składnika: `@code`

* Dodaj właściwość `IncrementAmount` publiczną `[Parameter]` dla z atrybutem.
* Zmień `IncrementCount` metodę, aby `IncrementAmount` użyć podczas `currentCount`zwiększania wartości .

*Strony/Counter.brzytwa*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Określ `IncrementAmount` element `Index` w `<Counter>` elemencie komponentu za pomocą atrybutu.

*Strony/Index.brzytwa*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Uruchom aplikację. Komponent `Index` ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy zaznaczony jest przycisk **Kliknij mnie.** Składnik `Counter` *(Counter.brzytwa)* w `/counter` dalszym ciągu zwiększa się o jeden.

## <a name="next-steps"></a>Następne kroki

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/templates>
* <xref:signalr/introduction>
