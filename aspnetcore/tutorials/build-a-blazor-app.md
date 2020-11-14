---
title: 'Tworzenie aplikacji z listą zadań do wykonania Blazor'
author: guardrex
description: 'Kompiluj Blazor aplikację krok po kroku.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 1efcd167d9a45b2def271b239c9b360749d72791
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570188"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="e3c52-103">Tworzenie aplikacji z listą zadań do wykonania Blazor</span><span class="sxs-lookup"><span data-stu-id="e3c52-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="e3c52-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e3c52-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e3c52-105">W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="e3c52-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="e3c52-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="e3c52-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e3c52-107">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="e3c52-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="e3c52-108">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="e3c52-108">Modify Razor components</span></span>
> * <span data-ttu-id="e3c52-109">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="e3c52-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="e3c52-110">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="e3c52-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="e3c52-111">Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="e3c52-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e3c52-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="e3c52-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="e3c52-113">Tworzenie aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="e3c52-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="e3c52-114">Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="e3c52-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="e3c52-115">Poprzednie polecenie tworzy folder o nazwie `TodoList` do przechowywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3c52-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="e3c52-116">`TodoList`Folder jest *folderem głównym* projektu.</span><span class="sxs-lookup"><span data-stu-id="e3c52-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="e3c52-117">Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e3c52-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="e3c52-118">Dodaj nowy `Todo` Razor składnik do aplikacji w `Pages` folderze przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="e3c52-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="e3c52-119">Razor nazwy plików składników wymagają wielkich liter.</span><span class="sxs-lookup"><span data-stu-id="e3c52-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="e3c52-120">Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` .</span><span class="sxs-lookup"><span data-stu-id="e3c52-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="e3c52-121">Nazwa pliku powinna być `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="e3c52-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="e3c52-122">W polu `Pages/Todo.razor` Podaj początkowe znaczniki dla składnika:</span><span class="sxs-lookup"><span data-stu-id="e3c52-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="e3c52-123">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="e3c52-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="e3c52-124">`NavMenu`Składnik ( `Shared/NavMenu.razor` ) jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3c52-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="e3c52-125">Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e3c52-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="e3c52-126">Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w `Shared/NavMenu.razor` pliku:</span><span class="sxs-lookup"><span data-stu-id="e3c52-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="e3c52-127">Skompiluj i uruchom aplikację, wykonując `dotnet run` polecenie w powłoce poleceń z `TodoList` folderu.</span><span class="sxs-lookup"><span data-stu-id="e3c52-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="e3c52-128">Odwiedź stronę nowych zadań do wykonania, `https://localhost:5001/todo` Aby upewnić się, że łącze do `Todo` składnika działa.</span><span class="sxs-lookup"><span data-stu-id="e3c52-128">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="e3c52-129">Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="e3c52-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="e3c52-130">Użyj następującego kodu w języku C# dla `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="e3c52-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="e3c52-131">Wróć do `Todo` składnika ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="e3c52-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="e3c52-132">Dodaj pole do zadań do wykonania w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="e3c52-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="e3c52-133">`Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="e3c52-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="e3c52-134">Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="e3c52-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="e3c52-135">Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="e3c52-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="e3c52-136">Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="e3c52-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="e3c52-137">Zatrzymaj uruchomioną aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="e3c52-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="e3c52-138">Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="e3c52-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="e3c52-139">Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="e3c52-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="e3c52-140">Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest połączony z przyciskiem.</span><span class="sxs-lookup"><span data-stu-id="e3c52-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="e3c52-141">Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="e3c52-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="e3c52-142">`AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="e3c52-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="e3c52-143">Aby uzyskać tytuł nowego elementu do wykonania, należy dodać `newTodo` pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="e3c52-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="e3c52-144">Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="e3c52-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="e3c52-145">Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:</span><span class="sxs-lookup"><span data-stu-id="e3c52-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="e3c52-146">Zatrzymaj uruchomioną aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="e3c52-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="e3c52-147">Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="e3c52-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="e3c52-148">Dodaj do listy zadań do zrobienia elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="e3c52-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="e3c52-149">Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone.</span><span class="sxs-lookup"><span data-stu-id="e3c52-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="e3c52-150">Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością.</span><span class="sxs-lookup"><span data-stu-id="e3c52-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="e3c52-151">Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="e3c52-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="e3c52-152">Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="e3c52-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="e3c52-153">Ukończony `Todo` składnik ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="e3c52-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="e3c52-154">Zatrzymaj uruchomioną aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="e3c52-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="e3c52-155">Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="e3c52-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="e3c52-156">Dodaj elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="e3c52-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e3c52-157">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="e3c52-157">Next steps</span></span>

<span data-ttu-id="e3c52-158">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="e3c52-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e3c52-159">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="e3c52-159">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="e3c52-160">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="e3c52-160">Modify Razor components</span></span>
> * <span data-ttu-id="e3c52-161">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="e3c52-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="e3c52-162">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="e3c52-162">Use routing in a Blazor app</span></span>

<span data-ttu-id="e3c52-163">Informacje o narzędziach dla ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="e3c52-163">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
