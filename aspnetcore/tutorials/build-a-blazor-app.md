---
title: Tworzenie aplikacji z listą zadań do wykonania Blazor
author: guardrex
description: Kompiluj Blazor aplikację krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: d984023a1c46c5383d47a1634c54e61747b83d60
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101213"
---
# <a name="build-a-blazor-todo-list-app"></a>Tworzenie aplikacji z listą zadań do wykonania Blazor

W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor
> * Modyfikuj Razor składniki
> * Używanie obsługi zdarzeń i powiązania danych w składnikach
> * Używanie routingu w Blazor aplikacji

Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.

## <a name="prerequisites"></a>Wymagania wstępne

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a>Tworzenie aplikacji z listą zadań do zrobienia Blazor

1. Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Poprzednie polecenie tworzy folder o nazwie `TodoList` z `-o|--output` opcją przechowywania aplikacji. `TodoList`Folder jest *folderem głównym* projektu. Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:

   ```dotnetcli
   cd TodoList
   ```

1. Dodaj nowy `Todo` Razor składnik do aplikacji przy użyciu następującego polecenia:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   `-n|--name`Opcja w poprzednim poleceniu określa nazwę nowego Razor składnika. Nowy składnik zostanie utworzony w `Pages` folderze projektu z `-o|--output` opcją.

   > [!IMPORTANT]
   > Razor nazwy plików składników wymagają wielkich liter. Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` . Nazwa pliku powinna być `Todo.razor` .

1. Otwórz `Todo` składnik w dowolnym edytorze plików i Dodaj `@page` Razor dyrektywę na początku pliku z względnym adresem URL `/todo` .

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   Zapisz plik `Pages/Todo.razor`.

1. Dodaj `Todo` składnik do paska nawigacyjnego.

   `NavMenu`Składnik jest używany w układzie aplikacji. Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji. `NavLink`Składnik udostępnia wskaźnik w interfejsie użytkownika aplikacji, gdy adres URL składnika jest ładowany przez aplikację.

   Na liście nieuporządkowanej ( `<ul>...</ul>` ) `NavMenu` składnika Dodaj następujący element listy ( `<li>...</li>` ) i `NavLink` składnik składnika `Todo` .

   W pliku `Shared/NavMenu.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   Zapisz plik `Shared/NavMenu.razor`.

1. Skompiluj i uruchom aplikację, wykonując [`dotnet watch run`](xref:tutorials/dotnet-watch) polecenie w powłoce poleceń z `TodoList` folderu. Po uruchomieniu aplikacji przejdź do strony Nowa strona do zrobienia, wybierając **`Todo`** link na pasku nawigacyjnym aplikacji, który ładuje stronę `/todo` .

   Pozostaw aplikację, na której działa powłoka poleceń. Za każdym razem, gdy plik jest zapisywany, aplikacja zostanie automatycznie ponownie skompilowana. Przeglądarka tymczasowo utraci połączenie z aplikacją podczas kompilowania i ponownego uruchamiania. Strona w przeglądarce zostanie automatycznie załadowana ponownie po ponownym nawiązaniu połączenia.

1. Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania. Użyj poniższego kodu w języku C# dla `TodoItem` klasy.

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > Jeśli używasz programu Visual Studio do utworzenia `ToDoItem.cs` pliku i `ToDoItem` klasy, użyj jednej z następujących metod:
   >
   > * Usuń przestrzeń nazw wygenerowanych przez program Visual Studio dla klasy.
   > * Użyj przycisku **Kopiuj** w poprzednim bloku kodu i Zastąp całą zawartość pliku wygenerowanego przez program Visual Studio.

1. Wróć do `Todo` składnika i wykonaj następujące zadania:

   * Dodaj pole do zadań do wykonania w `@code` bloku. `Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.
   * Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania. Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. Zapisz `TodoItem.cs` plik i zaktualizowany `Pages/Todo.razor` plik. W powłoce poleceń aplikacja zostanie automatycznie odbudowana po zapisaniu plików. Przeglądarka tymczasowo utraci połączenie z aplikacją, a następnie ponownie ładuje stronę po ponownym nawiązaniu połączenia.

1. Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ procedura obsługi zdarzeń nie jest dołączona do przycisku.

1. Dodaj `AddTodo` metodę do `Todo` składnika i Zarejestruj metodę dla przycisku przy użyciu `@onclick` atrybutu. `AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. Aby uzyskać tytuł nowego elementu do wykonania, Dodaj `newTodo` pole ciągu u góry `@code` bloku:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   Zmodyfikuj element tekstowy `<input>` , aby powiązać `newTodo` z `@bind` atrybutem:

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy. Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. Zapisz plik `Pages/Todo.razor`. Aplikacja zostanie automatycznie odbudowana w powłoce poleceń. Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.

1. Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone. Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością. Zmień `@todo.Title` na `<input>` element powiązany `todo.Title` z `@bind` :

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. Zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Ukończony `Todo` składnik ( `Pages/Todo.razor` ):

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. Zapisz plik `Pages/Todo.razor`. Aplikacja zostanie automatycznie odbudowana w powłoce poleceń. Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.

1. Dodaj elementy, Edytuj elementy i Oznacz gotowe elementy do przetestowania składnika.

1. Po zakończeniu zamknij aplikację w powłoce poleceń. Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor
> * Modyfikuj Razor składniki
> * Używanie obsługi zdarzeń i powiązania danych w składnikach
> * Używanie routingu w Blazor aplikacji

Informacje o narzędziach dla ASP.NET Core Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
