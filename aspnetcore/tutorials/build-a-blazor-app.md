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
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="5b6db-103">Tworzenie aplikacji z listą zadań do wykonania Blazor</span><span class="sxs-lookup"><span data-stu-id="5b6db-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="5b6db-104">W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b6db-104">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="5b6db-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="5b6db-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5b6db-106">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="5b6db-106">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="5b6db-107">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="5b6db-107">Modify Razor components</span></span>
> * <span data-ttu-id="5b6db-108">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="5b6db-108">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="5b6db-109">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="5b6db-109">Use routing in a Blazor app</span></span>

<span data-ttu-id="5b6db-110">Na końcu tego samouczka będziesz mieć działającą aplikację z listą zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b6db-110">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5b6db-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5b6db-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="5b6db-112">Tworzenie aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="5b6db-112">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="5b6db-113">Utwórz nową Blazor aplikację o nazwie `TodoList` w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="5b6db-113">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="5b6db-114">Poprzednie polecenie tworzy folder o nazwie `TodoList` z `-o|--output` opcją przechowywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b6db-114">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="5b6db-115">`TodoList`Folder jest *folderem głównym* projektu.</span><span class="sxs-lookup"><span data-stu-id="5b6db-115">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="5b6db-116">Zmień katalogi na `TodoList` folder za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b6db-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="5b6db-117">Dodaj nowy `Todo` Razor składnik do aplikacji przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="5b6db-117">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="5b6db-118">`-n|--name`Opcja w poprzednim poleceniu określa nazwę nowego Razor składnika.</span><span class="sxs-lookup"><span data-stu-id="5b6db-118">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="5b6db-119">Nowy składnik zostanie utworzony w `Pages` folderze projektu z `-o|--output` opcją.</span><span class="sxs-lookup"><span data-stu-id="5b6db-119">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="5b6db-120">Razor nazwy plików składników wymagają wielkich liter.</span><span class="sxs-lookup"><span data-stu-id="5b6db-120">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="5b6db-121">Otwórz `Pages` folder i upewnij się, że `Todo` Nazwa pliku składnika zaczyna się od wielkiej litery `T` .</span><span class="sxs-lookup"><span data-stu-id="5b6db-121">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="5b6db-122">Nazwa pliku powinna być `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="5b6db-122">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="5b6db-123">Otwórz `Todo` składnik w dowolnym edytorze plików i Dodaj `@page` Razor dyrektywę na początku pliku z względnym adresem URL `/todo` .</span><span class="sxs-lookup"><span data-stu-id="5b6db-123">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="5b6db-124">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="5b6db-124">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   <span data-ttu-id="5b6db-125">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="5b6db-125">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="5b6db-126">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="5b6db-126">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="5b6db-127">`NavMenu`Składnik jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b6db-127">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="5b6db-128">Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b6db-128">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="5b6db-129">`NavLink`Składnik udostępnia wskaźnik w interfejsie użytkownika aplikacji, gdy adres URL składnika jest ładowany przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b6db-129">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="5b6db-130">Na liście nieuporządkowanej ( `<ul>...</ul>` ) `NavMenu` składnika Dodaj następujący element listy ( `<li>...</li>` ) i `NavLink` składnik składnika `Todo` .</span><span class="sxs-lookup"><span data-stu-id="5b6db-130">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="5b6db-131">W pliku `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="5b6db-131">In `Shared/NavMenu.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   <span data-ttu-id="5b6db-132">Zapisz plik `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="5b6db-132">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="5b6db-133">Skompiluj i uruchom aplikację, wykonując [`dotnet watch run`](xref:tutorials/dotnet-watch) polecenie w powłoce poleceń z `TodoList` folderu.</span><span class="sxs-lookup"><span data-stu-id="5b6db-133">Build and run the app by executing the [`dotnet watch run`](xref:tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="5b6db-134">Po uruchomieniu aplikacji przejdź do strony Nowa strona do zrobienia, wybierając **`Todo`** link na pasku nawigacyjnym aplikacji, który ładuje stronę `/todo` .</span><span class="sxs-lookup"><span data-stu-id="5b6db-134">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="5b6db-135">Pozostaw aplikację, na której działa powłoka poleceń.</span><span class="sxs-lookup"><span data-stu-id="5b6db-135">Leave the app running the command shell.</span></span> <span data-ttu-id="5b6db-136">Za każdym razem, gdy plik jest zapisywany, aplikacja zostanie automatycznie ponownie skompilowana.</span><span class="sxs-lookup"><span data-stu-id="5b6db-136">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="5b6db-137">Przeglądarka tymczasowo utraci połączenie z aplikacją podczas kompilowania i ponownego uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="5b6db-137">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="5b6db-138">Strona w przeglądarce zostanie automatycznie załadowana ponownie po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="5b6db-138">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="5b6db-139">Dodaj `TodoItem.cs` plik do katalogu głównego projektu ( `TodoList` folderu) do przechowywania klasy, która reprezentuje element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b6db-139">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="5b6db-140">Użyj poniższego kodu w języku C# dla `TodoItem` klasy.</span><span class="sxs-lookup"><span data-stu-id="5b6db-140">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="5b6db-141">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="5b6db-141">`TodoItem.cs`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > <span data-ttu-id="5b6db-142">Jeśli używasz programu Visual Studio do utworzenia `ToDoItem.cs` pliku i `ToDoItem` klasy, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5b6db-142">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="5b6db-143">Usuń przestrzeń nazw wygenerowanych przez program Visual Studio dla klasy.</span><span class="sxs-lookup"><span data-stu-id="5b6db-143">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="5b6db-144">Użyj przycisku **Kopiuj** w poprzednim bloku kodu i Zastąp całą zawartość pliku wygenerowanego przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5b6db-144">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="5b6db-145">Wróć do `Todo` składnika i wykonaj następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="5b6db-145">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="5b6db-146">Dodaj pole do zadań do wykonania w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="5b6db-146">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="5b6db-147">`Todo`Składnik używa tego pola do obsługi stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b6db-147">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="5b6db-148">Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy ( `<li>` ).</span><span class="sxs-lookup"><span data-stu-id="5b6db-148">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="5b6db-149">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="5b6db-149">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. <span data-ttu-id="5b6db-150">Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="5b6db-150">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="5b6db-151">Dodaj tekst wejściowy ( `<input>` ) i przycisk ( `<button>` ) poniżej listy nieuporządkowanej ( `<ul>...</ul>` ):</span><span class="sxs-lookup"><span data-stu-id="5b6db-151">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. <span data-ttu-id="5b6db-152">Zapisz `TodoItem.cs` plik i zaktualizowany `Pages/Todo.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="5b6db-152">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="5b6db-153">W powłoce poleceń aplikacja zostanie automatycznie odbudowana po zapisaniu plików.</span><span class="sxs-lookup"><span data-stu-id="5b6db-153">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="5b6db-154">Przeglądarka tymczasowo utraci połączenie z aplikacją, a następnie ponownie ładuje stronę po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="5b6db-154">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="5b6db-155">Gdy **`Add todo`** przycisk jest zaznaczony, nic się nie dzieje, ponieważ procedura obsługi zdarzeń nie jest dołączona do przycisku.</span><span class="sxs-lookup"><span data-stu-id="5b6db-155">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="5b6db-156">Dodaj `AddTodo` metodę do `Todo` składnika i Zarejestruj metodę dla przycisku przy użyciu `@onclick` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5b6db-156">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="5b6db-157">`AddTodo`Metoda C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="5b6db-157">The `AddTodo` C# method is called when the button is selected:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. <span data-ttu-id="5b6db-158">Aby uzyskać tytuł nowego elementu do wykonania, Dodaj `newTodo` pole ciągu u góry `@code` bloku:</span><span class="sxs-lookup"><span data-stu-id="5b6db-158">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   <span data-ttu-id="5b6db-159">Zmodyfikuj element tekstowy `<input>` , aby powiązać `newTodo` z `@bind` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="5b6db-159">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="5b6db-160">Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="5b6db-160">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="5b6db-161">Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:</span><span class="sxs-lookup"><span data-stu-id="5b6db-161">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. <span data-ttu-id="5b6db-162">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="5b6db-162">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="5b6db-163">Aplikacja zostanie automatycznie odbudowana w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="5b6db-163">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="5b6db-164">Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="5b6db-164">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="5b6db-165">Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone.</span><span class="sxs-lookup"><span data-stu-id="5b6db-165">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="5b6db-166">Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością.</span><span class="sxs-lookup"><span data-stu-id="5b6db-166">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="5b6db-167">Zmień `@todo.Title` na `<input>` element powiązany `todo.Title` z `@bind` :</span><span class="sxs-lookup"><span data-stu-id="5b6db-167">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. <span data-ttu-id="5b6db-168">Zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone ( `IsDone` is `false` ).</span><span class="sxs-lookup"><span data-stu-id="5b6db-168">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="5b6db-169">Ukończony `Todo` składnik ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5b6db-169">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. <span data-ttu-id="5b6db-170">Zapisz plik `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="5b6db-170">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="5b6db-171">Aplikacja zostanie automatycznie odbudowana w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="5b6db-171">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="5b6db-172">Strona zostanie ponownie załadowana w przeglądarce po ponownym nawiązaniu połączenia z aplikacją przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="5b6db-172">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="5b6db-173">Dodaj elementy, Edytuj elementy i Oznacz gotowe elementy do przetestowania składnika.</span><span class="sxs-lookup"><span data-stu-id="5b6db-173">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="5b6db-174">Po zakończeniu zamknij aplikację w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="5b6db-174">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="5b6db-175">Wiele powłok poleceń akceptuje polecenie klawiatury <kbd>Ctrl</kbd> + <kbd>c</kbd> , aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="5b6db-175">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5b6db-176">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="5b6db-176">Next steps</span></span>

<span data-ttu-id="5b6db-177">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="5b6db-177">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5b6db-178">Tworzenie projektu aplikacji z listą zadań do zrobienia Blazor</span><span class="sxs-lookup"><span data-stu-id="5b6db-178">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="5b6db-179">Modyfikuj Razor składniki</span><span class="sxs-lookup"><span data-stu-id="5b6db-179">Modify Razor components</span></span>
> * <span data-ttu-id="5b6db-180">Używanie obsługi zdarzeń i powiązania danych w składnikach</span><span class="sxs-lookup"><span data-stu-id="5b6db-180">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="5b6db-181">Używanie routingu w Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="5b6db-181">Use routing in a Blazor app</span></span>

<span data-ttu-id="5b6db-182">Informacje o narzędziach dla ASP.NET Core Blazor :</span><span class="sxs-lookup"><span data-stu-id="5b6db-182">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
