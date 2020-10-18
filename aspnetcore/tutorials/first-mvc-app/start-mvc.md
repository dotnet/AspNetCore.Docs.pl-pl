---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
no-loc:
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 177112106d143a6826c1f927aac807da0aa9f2b4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113832"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="c1201-103">Wprowadzenie do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c1201-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="c1201-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c1201-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c1201-105">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c1201-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c1201-106">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="c1201-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="c1201-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="c1201-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1201-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="c1201-108">Create a web app.</span></span>
> * <span data-ttu-id="c1201-109">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="c1201-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c1201-110">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="c1201-110">Work with a database.</span></span>
> * <span data-ttu-id="c1201-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="c1201-111">Add search and validation.</span></span>

<span data-ttu-id="c1201-112">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="c1201-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c1201-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c1201-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1201-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1201-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1201-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1201-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1201-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c1201-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="c1201-117">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="c1201-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1201-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1201-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1201-119">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="c1201-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c1201-120">Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c1201-122">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c1201-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c1201-123">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="c1201-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="c1201-125">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="c1201-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="c1201-126">Z listy rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,1**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c1201-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="c1201-127">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="c1201-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="c1201-128">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="c1201-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c1201-129">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="c1201-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c1201-130">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="c1201-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1201-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1201-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1201-132">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="c1201-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c1201-133">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="c1201-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c1201-134">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c1201-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c1201-135">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="c1201-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c1201-136">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c1201-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c1201-137">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="c1201-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c1201-138">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="c1201-138">Select **Yes**</span></span>

  * <span data-ttu-id="c1201-139">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="c1201-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c1201-140">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c1201-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1201-141">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c1201-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1201-142">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="c1201-142">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c1201-144">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  aplikacja sieci Web**aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c1201-145">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c1201-147">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="c1201-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c1201-148">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="c1201-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c1201-149">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="c1201-149">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="c1201-150">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-150">Select **Next**.</span></span>

* <span data-ttu-id="c1201-151">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c1201-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="c1201-153">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c1201-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1201-154">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1201-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1201-155">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="c1201-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c1201-156">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1201-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c1201-157">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c1201-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c1201-158">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c1201-159">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="c1201-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c1201-160">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="c1201-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c1201-161">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="c1201-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c1201-162">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="c1201-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c1201-164">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="c1201-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="c1201-166">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="c1201-166">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1201-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1201-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1201-169">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="c1201-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c1201-170">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c1201-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c1201-171">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c1201-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c1201-172">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c1201-173">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c1201-174">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="c1201-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c1201-175">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="c1201-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1201-177">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c1201-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c1201-178">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1201-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c1201-179">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="c1201-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c1201-180">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c1201-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c1201-181">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c1201-182">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="c1201-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c1201-183">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="c1201-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c1201-184">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="c1201-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="c1201-185">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="c1201-185">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c1201-187">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="c1201-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c1201-188">Dalej</span><span class="sxs-lookup"><span data-stu-id="c1201-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c1201-189">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c1201-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c1201-190">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="c1201-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="c1201-191">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="c1201-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1201-192">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="c1201-192">Create a web app.</span></span>
> * <span data-ttu-id="c1201-193">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="c1201-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c1201-194">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="c1201-194">Work with a database.</span></span>
> * <span data-ttu-id="c1201-195">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="c1201-195">Add search and validation.</span></span>

<span data-ttu-id="c1201-196">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="c1201-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c1201-197">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="c1201-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1201-198">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1201-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1201-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1201-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1201-200">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c1201-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="c1201-201">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="c1201-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1201-202">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1201-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1201-203">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="c1201-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c1201-204">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c1201-206">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c1201-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c1201-207">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="c1201-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="c1201-209">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c1201-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="c1201-210">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="c1201-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="c1201-211">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="c1201-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c1201-212">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="c1201-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c1201-213">Jest to podstawowy projekt początkowy i jest dobrym miejscem do rozpoczęcia.</span><span class="sxs-lookup"><span data-stu-id="c1201-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1201-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1201-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1201-215">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="c1201-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c1201-216">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="c1201-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c1201-217">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c1201-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c1201-218">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="c1201-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c1201-219">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c1201-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c1201-220">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="c1201-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c1201-221">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="c1201-221">Select **Yes**</span></span>

  * <span data-ttu-id="c1201-222">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="c1201-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c1201-223">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c1201-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1201-224">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c1201-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1201-225">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="c1201-225">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c1201-227">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  aplikacja sieci Web**aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c1201-228">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="c1201-229">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="c1201-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c1201-230">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="c1201-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c1201-231">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="c1201-231">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="c1201-232">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="c1201-232">Select **Next**.</span></span>

* <span data-ttu-id="c1201-233">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c1201-233">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="c1201-234">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c1201-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1201-235">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1201-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1201-236">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="c1201-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c1201-237">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1201-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c1201-238">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c1201-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c1201-239">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c1201-240">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="c1201-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c1201-241">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="c1201-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c1201-242">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="c1201-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c1201-243">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="c1201-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c1201-245">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="c1201-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="c1201-247">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="c1201-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c1201-248">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="c1201-248">This app doesn't track personal information.</span></span> <span data-ttu-id="c1201-249">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c1201-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="c1201-251">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="c1201-251">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1201-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1201-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1201-254">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="c1201-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c1201-255">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c1201-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c1201-256">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c1201-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c1201-257">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c1201-258">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c1201-259">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="c1201-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c1201-260">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="c1201-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="c1201-261">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="c1201-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c1201-262">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="c1201-262">This app doesn't track personal information.</span></span> <span data-ttu-id="c1201-263">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c1201-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="c1201-265">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="c1201-265">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1201-267">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c1201-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c1201-268">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1201-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c1201-269">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="c1201-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c1201-270">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c1201-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c1201-271">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="c1201-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c1201-272">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="c1201-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c1201-273">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="c1201-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c1201-274">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="c1201-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="c1201-275">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="c1201-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c1201-276">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="c1201-276">This app doesn't track personal information.</span></span> <span data-ttu-id="c1201-277">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c1201-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="c1201-279">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="c1201-279">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c1201-281">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="c1201-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c1201-282">Dalej</span><span class="sxs-lookup"><span data-stu-id="c1201-282">Next</span></span>](adding-controller.md)

::: moniker-end
