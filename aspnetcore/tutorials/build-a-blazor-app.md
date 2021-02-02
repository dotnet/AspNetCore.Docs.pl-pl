---
title: Tworzenie aplikacji z listą zadań do wykonania Blazor
author: guardrex
description: Kompiluj Blazor aplikację krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
ms.openlocfilehash: 106e1119db777074b5eae24f5d7e216e6127ca13
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238307"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="3d2f4-103">Tworzenie aplikacji z listą zadań do wykonania Blazor</span><span class="sxs-lookup"><span data-stu-id="3d2f4-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="3d2f4-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d2f4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3d2f4-105">W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="3d2f4-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3d2f4-107">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="3d2f4-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="3d2f4-108">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="3d2f4-108">Modify Razor components</span></span>
> * <span data-ttu-id="3d2f4-109">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="3d2f4-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="3d2f4-110">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="3d2f4-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="3d2f4-111">Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3d2f4-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3d2f4-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="3d2f4-113">Tworzenie aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="3d2f4-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="3d2f4-114">Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="3d2f4-115">Poprzednie polecenie tworzy folder o nazwie `TodoList` z `-o|--output` opcją przechowywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="3d2f4-116">`TodoList`Folder jest *folderem głównym* projektu.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="3d2f4-117">Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="3d2f4-118">Dodaj nowy `Todo` Razor składnik do aplikacji przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="3d2f4-119">`-n|--name`Opcja w poprzednim poleceniu określa nazwę nowego Razor składnika.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="3d2f4-120">Nowy składnik zostanie utworzony w `Pages` folderze projektu z `-o|--output` opcją.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="3d2f4-121">Razor nazwy plików składników wymagają wielkich liter.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="3d2f4-122">Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` .</span><span class="sxs-lookup"><span data-stu-id="3d2f4-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="3d2f4-123">Nazwa pliku powinna być `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="3d2f4-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="3d2f4-124">Otwórz `Todo` składnik w dowolnym edytorze plików i Dodaj `@page` Razor dyrektywę na początku pliku z względnym adresem URL `/todo` .</span><span class="sxs-lookup"><span data-stu-id="3d2f4-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="3d2f4-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   <span data-ttu-id="3d2f4-126">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="3d2f4-127">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="3d2f4-128">`NavMenu`Składnik jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="3d2f4-129">Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="3d2f4-130">`NavLink`Składnik udostępnia wskaźnik w interfejsie użytkownika aplikacji, gdy adres URL składnika jest ładowany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="3d2f4-131">Na liście nieuporządkowanej ( `<ul>...</ul>` ) `NavMenu` składnika Dodaj następujący element listy ( `<li>...</li>` ) i `NavLink` składnik składnika `Todo` .</span><span class="sxs-lookup"><span data-stu-id="3d2f4-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="3d2f4-132">W pliku `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="3d2f4-133">Zapisz plik `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="3d2f4-134">Skompiluj i uruchom aplikację, wykonując [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) polecenie w powłoce poleceń z `TodoList` folderu.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="3d2f4-135">Po uruchomieniu aplikacji przejdź do strony Nowa strona do zrobienia, wybierając **`Todo`** link na pasku nawigacyjnym aplikacji, który ładuje stronę `/todo` .</span><span class="sxs-lookup"><span data-stu-id="3d2f4-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="3d2f4-136">Pozostaw aplikację, na której działa powłoka poleceń.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-136">Leave the app running the command shell.</span></span> <span data-ttu-id="3d2f4-137">Za każdym razem, gdy plik jest zapisywany, aplikacja zostanie automatycznie ponownie skompilowana.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="3d2f4-138">Przeglądarka tymczasowo utraci połączenie z aplikacją podczas kompilowania i ponownego uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="3d2f4-139">Strona w przeglądarce zostanie automatycznie załadowana ponownie po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="3d2f4-140">Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="3d2f4-141">Użyj poniższego kodu w języku C# dla `TodoItem` klasy.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="3d2f4-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > <span data-ttu-id="3d2f4-143">Jeśli używasz programu Visual Studio do utworzenia `ToDoItem.cs` pliku i `ToDoItem` klasy, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-143">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="3d2f4-144">Usuń przestrzeń nazw wygenerowanych przez program Visual Studio dla klasy.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-144">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="3d2f4-145">Użyj przycisku **Kopiuj** w poprzednim bloku kodu i Zastąp całą zawartość pliku wygenerowanego przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-145">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="3d2f4-146">Wróć do `Todo` składnika i wykonaj następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-146">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="3d2f4-147">Dodaj pole do zadań do wykonania w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-147">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="3d2f4-148">`Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-148">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="3d2f4-149">Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="3d2f4-149">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="3d2f4-150">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-150">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="3d2f4-151">Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-151">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="3d2f4-152">Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="3d2f4-152">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. <span data-ttu-id="3d2f4-153">Zapisz `TodoItem.cs` plik i zaktualizowany `Pages/Todo.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-153">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="3d2f4-154">W powłoce poleceń aplikacja zostanie automatycznie odbudowana po zapisaniu plików.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-154">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="3d2f4-155">Przeglądarka tymczasowo utraci połączenie z aplikacją, a następnie ponownie ładuje stronę po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-155">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="3d2f4-156">Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ procedura obsługi zdarzeń nie jest dołączona do przycisku.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-156">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="3d2f4-157">Dodaj `AddTodo` metodę do `Todo` składnika i Zarejestruj metodę dla przycisku przy użyciu `@onclick` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-157">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="3d2f4-158">`AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-158">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="3d2f4-159">Aby uzyskać tytuł nowego elementu do wykonania, Dodaj `newTodo` pole ciągu u góry `@code` bloku:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-159">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   <span data-ttu-id="3d2f4-160">Zmodyfikuj element tekstowy `<input>` , aby powiązać `newTodo` z `@bind` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-160">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="3d2f4-161">Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-161">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="3d2f4-162">Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-162">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. <span data-ttu-id="3d2f4-163">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-163">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="3d2f4-164">Aplikacja zostanie automatycznie odbudowana w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-164">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="3d2f4-165">Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-165">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="3d2f4-166">Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-166">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="3d2f4-167">Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-167">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="3d2f4-168">Zmień `@todo.Title` na `<input>` element powiązany `todo.Title` z `@bind` :</span><span class="sxs-lookup"><span data-stu-id="3d2f4-168">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. <span data-ttu-id="3d2f4-169">Zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="3d2f4-169">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="3d2f4-170">Ukończony `Todo` składnik ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="3d2f4-170">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="3d2f4-171">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-171">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="3d2f4-172">Aplikacja zostanie automatycznie odbudowana w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-172">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="3d2f4-173">Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-173">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="3d2f4-174">Dodaj elementy, Edytuj elementy i Oznacz gotowe elementy do przetestowania składnika.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-174">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="3d2f4-175">Po zakończeniu zamknij aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-175">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="3d2f4-176">Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="3d2f4-176">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3d2f4-177">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="3d2f4-177">Next steps</span></span>

<span data-ttu-id="3d2f4-178">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="3d2f4-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3d2f4-179">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="3d2f4-179">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="3d2f4-180">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="3d2f4-180">Modify Razor components</span></span>
> * <span data-ttu-id="3d2f4-181">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="3d2f4-181">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="3d2f4-182">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="3d2f4-182">Use routing in a Blazor app</span></span>

<span data-ttu-id="3d2f4-183">Informacje o narzędziach dla ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="3d2f4-183">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
