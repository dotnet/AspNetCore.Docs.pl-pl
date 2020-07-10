---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: d4eb1744b1186704603430584b3da0793f90ee49
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213084"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="5d67f-103">Wprowadzenie do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5d67f-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="5d67f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5d67f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5d67f-105">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5d67f-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5d67f-106">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="5d67f-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="5d67f-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5d67f-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="5d67f-108">Create a web app.</span></span>
> * <span data-ttu-id="5d67f-109">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5d67f-110">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="5d67f-110">Work with a database.</span></span>
> * <span data-ttu-id="5d67f-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="5d67f-111">Add search and validation.</span></span>

<span data-ttu-id="5d67f-112">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5d67f-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5d67f-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d67f-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d67f-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d67f-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d67f-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d67f-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5d67f-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="5d67f-117">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="5d67f-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d67f-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d67f-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d67f-119">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5d67f-120">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5d67f-122">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5d67f-123">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="5d67f-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="5d67f-125">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="5d67f-126">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="5d67f-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="5d67f-127">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="5d67f-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5d67f-128">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="5d67f-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5d67f-129">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="5d67f-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d67f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d67f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d67f-131">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="5d67f-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5d67f-132">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="5d67f-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5d67f-133">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5d67f-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5d67f-134">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="5d67f-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5d67f-135">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5d67f-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5d67f-136">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="5d67f-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5d67f-137">Wybierz opcję **tak**</span><span class="sxs-lookup"><span data-stu-id="5d67f-137">Select **Yes**</span></span>

  * <span data-ttu-id="5d67f-138">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="5d67f-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5d67f-139">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5d67f-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d67f-140">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5d67f-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5d67f-141">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-141">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5d67f-143">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  aplikacja sieci Web**aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5d67f-144">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="5d67f-146">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="5d67f-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5d67f-147">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5d67f-148">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="5d67f-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="5d67f-149">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-149">Select **Next**.</span></span>

* <span data-ttu-id="5d67f-150">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="5d67f-152">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5d67f-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d67f-153">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d67f-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d67f-154">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="5d67f-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5d67f-155">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d67f-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5d67f-156">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5d67f-157">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5d67f-158">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="5d67f-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5d67f-159">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5d67f-160">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="5d67f-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5d67f-161">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="5d67f-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5d67f-163">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d67f-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="5d67f-165">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="5d67f-165">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d67f-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d67f-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d67f-168">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="5d67f-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5d67f-169">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5d67f-170">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5d67f-171">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5d67f-172">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5d67f-173">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5d67f-174">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="5d67f-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d67f-176">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5d67f-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5d67f-177">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d67f-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5d67f-178">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5d67f-179">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5d67f-180">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5d67f-181">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="5d67f-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5d67f-182">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5d67f-183">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="5d67f-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="5d67f-184">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="5d67f-184">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5d67f-186">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="5d67f-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5d67f-187">Dalej</span><span class="sxs-lookup"><span data-stu-id="5d67f-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="5d67f-188">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5d67f-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5d67f-189">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="5d67f-190">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="5d67f-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5d67f-191">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="5d67f-191">Create a web app.</span></span>
> * <span data-ttu-id="5d67f-192">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="5d67f-193">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="5d67f-193">Work with a database.</span></span>
> * <span data-ttu-id="5d67f-194">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="5d67f-194">Add search and validation.</span></span>

<span data-ttu-id="5d67f-195">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="5d67f-196">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5d67f-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d67f-197">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d67f-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d67f-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d67f-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d67f-199">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5d67f-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="5d67f-200">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="5d67f-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d67f-201">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d67f-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5d67f-202">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="5d67f-203">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="5d67f-205">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="5d67f-206">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="5d67f-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="5d67f-208">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="5d67f-209">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="5d67f-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="5d67f-210">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="5d67f-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="5d67f-211">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="5d67f-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="5d67f-212">Jest to podstawowy projekt początkowy i jest dobrym miejscem do rozpoczęcia.</span><span class="sxs-lookup"><span data-stu-id="5d67f-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d67f-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d67f-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d67f-214">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="5d67f-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="5d67f-215">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="5d67f-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="5d67f-216">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5d67f-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5d67f-217">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="5d67f-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="5d67f-218">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5d67f-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="5d67f-219">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="5d67f-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="5d67f-220">Wybierz opcję **tak**</span><span class="sxs-lookup"><span data-stu-id="5d67f-220">Select **Yes**</span></span>

  * <span data-ttu-id="5d67f-221">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="5d67f-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="5d67f-222">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5d67f-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d67f-223">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5d67f-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5d67f-224">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-224">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5d67f-226">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  aplikacja sieci Web**aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="5d67f-227">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="5d67f-228">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="5d67f-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="5d67f-229">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="5d67f-230">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="5d67f-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="5d67f-231">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-231">Select **Next**.</span></span>

* <span data-ttu-id="5d67f-232">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5d67f-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="5d67f-233">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5d67f-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5d67f-234">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d67f-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5d67f-235">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="5d67f-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="5d67f-236">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d67f-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5d67f-237">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5d67f-238">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5d67f-239">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="5d67f-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="5d67f-240">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5d67f-241">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="5d67f-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="5d67f-242">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="5d67f-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="5d67f-244">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d67f-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="5d67f-246">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5d67f-247">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="5d67f-247">This app doesn't track personal information.</span></span> <span data-ttu-id="5d67f-248">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5d67f-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="5d67f-250">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="5d67f-250">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5d67f-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d67f-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5d67f-253">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="5d67f-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5d67f-254">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="5d67f-255">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="5d67f-256">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5d67f-257">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="5d67f-258">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="5d67f-259">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="5d67f-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="5d67f-260">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5d67f-261">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="5d67f-261">This app doesn't track personal information.</span></span> <span data-ttu-id="5d67f-262">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5d67f-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="5d67f-264">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="5d67f-264">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5d67f-266">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5d67f-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5d67f-267">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5d67f-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5d67f-268">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5d67f-269">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="5d67f-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5d67f-270">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="5d67f-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="5d67f-271">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="5d67f-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="5d67f-272">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="5d67f-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="5d67f-273">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="5d67f-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="5d67f-274">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="5d67f-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="5d67f-275">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="5d67f-275">This app doesn't track personal information.</span></span> <span data-ttu-id="5d67f-276">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="5d67f-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="5d67f-278">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="5d67f-278">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="5d67f-280">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="5d67f-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5d67f-281">Dalej</span><span class="sxs-lookup"><span data-stu-id="5d67f-281">Next</span></span>](adding-controller.md)

::: moniker-end
