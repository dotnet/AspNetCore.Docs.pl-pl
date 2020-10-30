---
title: 'Tworzenie aplikacji z listą zadań do wykonania :::no-loc(Blazor):::'
author: guardrex
description: 'Kompiluj :::no-loc(Blazor)::: aplikację krok po kroku.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 68a38b82f5a89365e4f345a60f1f34b697c027ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060095"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="eeae4-103">Tworzenie aplikacji z listą zadań do wykonania :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="eeae4-103">Build a :::no-loc(Blazor)::: todo list app</span></span>

<span data-ttu-id="eeae4-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eeae4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eeae4-105">W tym samouczku pokazano, jak utworzyć i zmodyfikować :::no-loc(Blazor)::: aplikację.</span><span class="sxs-lookup"><span data-stu-id="eeae4-105">This tutorial shows you how to build and modify a :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="eeae4-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="eeae4-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eeae4-107">Tworzenie projektu aplikacji z listą zadań do zrobienia :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="eeae4-107">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="eeae4-108">Modyfikuj :::no-loc(Razor)::: składniki</span><span class="sxs-lookup"><span data-stu-id="eeae4-108">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="eeae4-109">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="eeae4-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="eeae4-110">Używanie routingu w :::no-loc(Blazor)::: aplikacji</span><span class="sxs-lookup"><span data-stu-id="eeae4-110">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="eeae4-111">Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="eeae4-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eeae4-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="eeae4-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="eeae4-113">Tworzenie aplikacji z listą zadań do zrobienia :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="eeae4-113">Create a todo list :::no-loc(Blazor)::: app</span></span>

1. <span data-ttu-id="eeae4-114">Utwórz nową :::no-loc(Blazor)::: aplikację o nazwie `TodoList` w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="eeae4-114">Create a new :::no-loc(Blazor)::: app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="eeae4-115">Poprzednie polecenie tworzy folder o nazwie `TodoList` do przechowywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eeae4-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="eeae4-116">`TodoList`Folder jest *folderem głównym* projektu.</span><span class="sxs-lookup"><span data-stu-id="eeae4-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="eeae4-117">Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="eeae4-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="eeae4-118">Dodaj nowy `Todo` :::no-loc(Razor)::: składnik do aplikacji w `Pages` folderze przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="eeae4-118">Add a new `Todo` :::no-loc(Razor)::: component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="eeae4-119">:::no-loc(Razor)::: nazwy plików składników wymagają wielkich liter.</span><span class="sxs-lookup"><span data-stu-id="eeae4-119">:::no-loc(Razor)::: component file names require a capitalized first letter.</span></span> <span data-ttu-id="eeae4-120">Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` .</span><span class="sxs-lookup"><span data-stu-id="eeae4-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="eeae4-121">Nazwa pliku powinna być `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="eeae4-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="eeae4-122">W polu `Pages/Todo.razor` Podaj początkowe znaczniki dla składnika:</span><span class="sxs-lookup"><span data-stu-id="eeae4-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="eeae4-123">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="eeae4-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="eeae4-124">`NavMenu`Składnik ( `Shared/NavMenu.razor` ) jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eeae4-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="eeae4-125">Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eeae4-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="eeae4-126">Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w `Shared/NavMenu.razor` pliku:</span><span class="sxs-lookup"><span data-stu-id="eeae4-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="eeae4-127">Skompiluj i uruchom aplikację, wykonując `dotnet run` polecenie w powłoce poleceń z `TodoList` folderu.</span><span class="sxs-lookup"><span data-stu-id="eeae4-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="eeae4-128">Odwiedź stronę nowych zadań do wykonania, aby upewnić się, że łącze do `Todo` składnika działa.</span><span class="sxs-lookup"><span data-stu-id="eeae4-128">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="eeae4-129">Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="eeae4-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="eeae4-130">Użyj następującego kodu w języku C# dla `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="eeae4-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="eeae4-131">Wróć do `Todo` składnika ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="eeae4-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="eeae4-132">Dodaj pole do zadań do wykonania w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="eeae4-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="eeae4-133">`Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="eeae4-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="eeae4-134">Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="eeae4-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="eeae4-135">Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="eeae4-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="eeae4-136">Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="eeae4-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="eeae4-137">Zatrzymaj uruchomioną aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="eeae4-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="eeae4-138">Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="eeae4-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="eeae4-139">Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="eeae4-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="eeae4-140">Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest połączony z przyciskiem.</span><span class="sxs-lookup"><span data-stu-id="eeae4-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="eeae4-141">Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="eeae4-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="eeae4-142">`AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="eeae4-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="eeae4-143">Aby uzyskać tytuł nowego elementu do wykonania, należy dodać `newTodo` pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="eeae4-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="eeae4-144">Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="eeae4-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="eeae4-145">Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:</span><span class="sxs-lookup"><span data-stu-id="eeae4-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="eeae4-146">Zatrzymaj uruchomioną aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="eeae4-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="eeae4-147">Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="eeae4-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="eeae4-148">Dodaj do listy zadań do zrobienia elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="eeae4-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="eeae4-149">Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone.</span><span class="sxs-lookup"><span data-stu-id="eeae4-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="eeae4-150">Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością.</span><span class="sxs-lookup"><span data-stu-id="eeae4-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="eeae4-151">Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="eeae4-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="eeae4-152">Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="eeae4-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="eeae4-153">Ukończony `Todo` składnik ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="eeae4-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="eeae4-154">Zatrzymaj uruchomioną aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="eeae4-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="eeae4-155">Skompiluj ponownie i uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="eeae4-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="eeae4-156">Dodaj elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="eeae4-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eeae4-157">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="eeae4-157">Next steps</span></span>

<span data-ttu-id="eeae4-158">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="eeae4-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eeae4-159">Tworzenie projektu aplikacji z listą zadań do zrobienia :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="eeae4-159">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="eeae4-160">Modyfikuj :::no-loc(Razor)::: składniki</span><span class="sxs-lookup"><span data-stu-id="eeae4-160">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="eeae4-161">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="eeae4-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="eeae4-162">Używanie routingu w :::no-loc(Blazor)::: aplikacji</span><span class="sxs-lookup"><span data-stu-id="eeae4-162">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="eeae4-163">Informacje o narzędziach dla ASP.NET Core :::no-loc(Blazor)::: :</span><span class="sxs-lookup"><span data-stu-id="eeae4-163">Learn about tooling for ASP.NET Core :::no-loc(Blazor)::::</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
