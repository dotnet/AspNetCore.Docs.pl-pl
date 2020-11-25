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
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="c1ea8-103">Tworzenie aplikacji z listą zadań do wykonania Blazor</span><span class="sxs-lookup"><span data-stu-id="c1ea8-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="c1ea8-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1ea8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c1ea8-105">W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="c1ea8-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1ea8-107">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="c1ea8-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="c1ea8-108">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="c1ea8-108">Modify Razor components</span></span>
> * <span data-ttu-id="c1ea8-109">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="c1ea8-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="c1ea8-110">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="c1ea8-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="c1ea8-111">Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c1ea8-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c1ea8-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="c1ea8-113">Tworzenie aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="c1ea8-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="c1ea8-114">Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="c1ea8-115">Poprzednie polecenie tworzy folder o nazwie `TodoList` do przechowywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="c1ea8-116">`TodoList`Folder jest *folderem głównym* projektu.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="c1ea8-117">Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="c1ea8-118">Dodaj nowy `Todo` Razor składnik do aplikacji w `Pages` folderze przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="c1ea8-119">Razor nazwy plików składników wymagają wielkich liter.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="c1ea8-120">Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` .</span><span class="sxs-lookup"><span data-stu-id="c1ea8-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="c1ea8-121">Nazwa pliku powinna być `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="c1ea8-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="c1ea8-122">W polu `Pages/Todo.razor` Podaj początkowe znaczniki dla składnika:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="c1ea8-123">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="c1ea8-124">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="c1ea8-125">`NavMenu`Składnik ( `Shared/NavMenu.razor` ) jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="c1ea8-126">Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="c1ea8-127">Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w `Shared/NavMenu.razor` pliku:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="c1ea8-128">Zapisz plik `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="c1ea8-129">Skompiluj i uruchom aplikację, wykonując [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) polecenie w powłoce poleceń z `TodoList` folderu.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="c1ea8-130">Odwiedź stronę nowych zadań do wykonania pod adresem, `https://localhost:5001/todo` Aby upewnić się, że link nawigacyjny do `Todo` składnika działa.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="c1ea8-131">Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="c1ea8-132">Użyj następującego kodu w języku C# dla `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="c1ea8-133">Wróć do `Todo` składnika ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c1ea8-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="c1ea8-134">Dodaj pole do zadań do wykonania w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="c1ea8-135">`Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="c1ea8-136">Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="c1ea8-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="c1ea8-137">Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="c1ea8-138">Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="c1ea8-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="c1ea8-139">Zapisz `TodoItem.cs` plik i zaktualizowany `Pages/Todo.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="c1ea8-140">W powłoce poleceń aplikacja zostanie automatycznie odbudowana po zapisaniu plików.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="c1ea8-141">Przeglądarka tymczasowo utraci połączenie z aplikacją, a następnie ponownie ładuje stronę po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="c1ea8-142">Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ procedura obsługi zdarzeń nie jest dołączona do przycisku.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="c1ea8-143">Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="c1ea8-144">`AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="c1ea8-145">Aby uzyskać tytuł nowego elementu do wykonania, należy dodać `newTodo` pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="c1ea8-146">Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="c1ea8-147">Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="c1ea8-148">Zapisz plik `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="c1ea8-149">Aplikacja zostanie automatycznie odbudowana w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="c1ea8-150">Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="c1ea8-151">Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="c1ea8-152">Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="c1ea8-153">Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="c1ea8-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="c1ea8-154">Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="c1ea8-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="c1ea8-155">Ukończony `Todo` składnik ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c1ea8-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="c1ea8-156">Zapisz plik `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="c1ea8-157">Aplikacja zostanie automatycznie odbudowana w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="c1ea8-158">Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="c1ea8-159">Dodaj elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="c1ea8-160">Po zakończeniu zamknij aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="c1ea8-161">Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1ea8-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c1ea8-162">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="c1ea8-162">Next steps</span></span>

<span data-ttu-id="c1ea8-163">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="c1ea8-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1ea8-164">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="c1ea8-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="c1ea8-165">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="c1ea8-165">Modify Razor components</span></span>
> * <span data-ttu-id="c1ea8-166">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="c1ea8-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="c1ea8-167">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="c1ea8-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="c1ea8-168">Informacje o narzędziach dla ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="c1ea8-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
