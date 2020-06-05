---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij korzystać z programu Blazor , tworząc Blazor aplikację przy użyciu wybranego przez siebie narzędzia.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452034"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Wprowadzenie do ASP.NET CoreBlazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

Aby rozpocząć pracę z programem Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .

1. Tworzenie nowego projektu.

1. Wybierz pozycję ** Blazor aplikacja**. Wybierz pozycję **Dalej**.

1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. W przypadku Blazor środowiska webassembly wybierz szablon ** Blazor aplikacji webassembly** . Aby Blazor skorzystać z serwera, wybierz szablon ** Blazor aplikacja serwera** . Wybierz przycisk **Utwórz**.

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu, * Blazor zestawu webassembly* i * Blazor serwera*, zobacz <xref:blazor/hosting-models> .

1. Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet --version
   ```

1. Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/).

1. Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .

  Aby ustawić `debug.javascript.usePreview` `true` przy użyciu interfejsu użytkownika vs Code, Otwórz **File**  >  **Preferences**  >  **Ustawienia** preferencji plików i Wyszukaj `debug javascript use preview` . Zaznacz pole wyboru **Użyj nowego debugera JavaScript w wersji zapoznawczej dla środowiska Node. js i programu Chrome**.

1. W przypadku Blazor środowiska webassembly wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   W przypadku Blazor środowiska serwerowego wykonaj następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu, * Blazor zestawu webassembly* i * Blazor serwera*, zobacz <xref:blazor/hosting-models> .

1. Otwórz folder *WebApplication1* w Visual Studio Code.

1. Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu. Wybierz pozycję **Tak**.

1. Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.

   W przypadku Blazor środowiska webassembly wybierz szablon ** Blazor aplikacji webassembly** . Aby Blazor skorzystać z serwera, wybierz szablon ** Blazor aplikacja serwera** . Wybierz pozycję **Dalej**.

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu, * Blazor zestawu webassembly* i * Blazor serwera*, zobacz <xref:blazor/hosting-models> .

1. Potwierdź następujące konfiguracje:

   * **Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.
   * **Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
   
   Wybierz pozycję **Dalej**.

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` . Wybierz przycisk **Utwórz**.

1. Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*. Uruchom **aplikację z**  >  przyciskiem Uruchom**debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.

Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

1. Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:

   ```dotnetcli
   dotnet --version
   ```

1. W przypadku Blazor środowiska webassembly wykonaj następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   W celu Blazor korzystania z serwera wykonaj następujące polecenia w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Aby uzyskać informacje na temat dwóch Blazor modeli hostingu, * Blazor zestawu webassembly* i * Blazor serwera*, zobacz <xref:blazor/hosting-models> .

1. W przeglądarce przejdź do `https://localhost:5001` .

---

Na pasku bocznym są dostępne wiele stron:

* Domowy
* Licznik
* Pobieranie danych

Na stronie licznik wybierz przycisk **kliknij** , aby zwiększyć licznik bez odświeżania strony. Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript, ale przy użyciu Blazor języka C#.

*Pages/Counter. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Żądanie `/counter` w przeglądarce, zgodnie z definicją w `@page` dyrektywie u góry, powoduje, że `Counter` składnik renderuje jego zawartość. Składniki są renderowane w postaci reprezentacji drzewa renderowania, która może być następnie używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.

Za każdym razem, gdy zostanie wybrany przycisk **kliknij mnie** :

* `onclick`Zdarzenie jest wyzwalane.
* `IncrementCount`Metoda jest wywoływana.
* Wartość `currentCount` jest zwiększana.
* Składnik jest ponownie renderowany.

Środowisko uruchomieniowe porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do Document Object Model (DOM).

Dodaj składnik do innego składnika przy użyciu składni języka HTML. Na przykład Dodaj `Counter` składnik do strony głównej aplikacji przez dodanie `<Counter />` elementu do `Index` składnika.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Uruchom aplikację. Strona główna ma swój własny licznik dostarczony przez `Counter` składnik.

Parametry składnika są określone przy użyciu atrybutów lub [zawartości podrzędnej](xref:blazor/components#child-content), które umożliwiają ustawianie właściwości składnika podrzędnego. Aby dodać parametr do `Counter` składnika, zaktualizuj `@code` blok składnika:

* Dodaj właściwość publiczną dla `IncrementAmount` [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atrybutu.
* Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` podczas zwiększania wartości `currentCount` .

*Pages/Counter. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Określ `IncrementAmount` `Index` element w elemencie składnika `<Counter>` przy użyciu atrybutu.

*Pages/index. Razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Uruchom aplikację. `Index`Składnik ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy jest zaznaczony przycisk **kliknij mnie** . `Counter`Składnik (*Counter. Razor*) w `/counter` dalszym ciągu zwiększa się o jeden.

## <a name="next-steps"></a>Następne kroki

Tworzenie Blazor aplikacji krok po kroku:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/templates>
* <xref:signalr/introduction>
