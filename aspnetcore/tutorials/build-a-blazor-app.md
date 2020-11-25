---
title: Tworzenie aplikacji z listą zadań do wykonania Blazor
author: guardrex
description: Kompiluj Blazor aplikację krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870441"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Tworzenie aplikacji z listą zadań do wykonania Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

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

## <a name="create-a-todo-list-no-locblazor-app"></a>Tworzenie aplikacji z listą zadań do zrobienia Blazor

1. Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Poprzednie polecenie tworzy folder o nazwie `TodoList` do przechowywania aplikacji. `TodoList`Folder jest *folderem głównym* projektu. Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:

   ```dotnetcli
   cd TodoList
   ```

1. Dodaj nowy `Todo` Razor składnik do aplikacji w `Pages` folderze przy użyciu następującego polecenia:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor nazwy plików składników wymagają wielkich liter. Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` . Nazwa pliku powinna być `Todo.razor` .

1. W polu `Pages/Todo.razor` Podaj początkowe znaczniki dla składnika:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   Zapisz plik `Pages/Todo.razor`.

1. Dodaj `Todo` składnik do paska nawigacyjnego.

   `NavMenu`Składnik ( `Shared/NavMenu.razor` ) jest używany w układzie aplikacji. Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.

   Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w `Shared/NavMenu.razor` pliku:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   Zapisz plik `Shared/NavMenu.razor`.

1. Skompiluj i uruchom aplikację, wykonując [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) polecenie w powłoce poleceń z `TodoList` folderu. Odwiedź stronę nowych zadań do wykonania pod adresem, `https://localhost:5001/todo` Aby upewnić się, że link nawigacyjny do `Todo` składnika działa.

1. Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania. Użyj następującego kodu w języku C# dla `TodoItem` klasy:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Wróć do `Todo` składnika ( `Pages/Todo.razor` ):

   * Dodaj pole do zadań do wykonania w `@code` bloku. `Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.
   * Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania. Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Zapisz `TodoItem.cs` plik i zaktualizowany `Pages/Todo.razor` plik. W powłoce poleceń aplikacja zostanie automatycznie odbudowana po zapisaniu plików. Przeglądarka tymczasowo utraci połączenie z aplikacją, a następnie ponownie ładuje stronę po ponownym nawiązaniu połączenia.

1. Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ procedura obsługi zdarzeń nie jest dołączona do przycisku.

1. Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu. `AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Aby uzyskać tytuł nowego elementu do wykonania, należy dodać `newTodo` pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy. Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Zapisz plik `Pages/ToDo.razor`. Aplikacja zostanie automatycznie odbudowana w powłoce poleceń. Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.

1. Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone. Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością. Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Ukończony `Todo` składnik ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Zapisz plik `Pages/ToDo.razor`. Aplikacja zostanie automatycznie odbudowana w powłoce poleceń. Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.

1. Dodaj elementy do wykonania, aby przetestować nowy kod.

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
