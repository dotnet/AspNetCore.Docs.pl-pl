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
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629993"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="d2b06-103">Wprowadzenie do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d2b06-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="d2b06-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d2b06-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d2b06-105">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d2b06-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d2b06-106">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="d2b06-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="d2b06-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2b06-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="d2b06-108">Create a web app.</span></span>
> * <span data-ttu-id="d2b06-109">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d2b06-110">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="d2b06-110">Work with a database.</span></span>
> * <span data-ttu-id="d2b06-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="d2b06-111">Add search and validation.</span></span>

<span data-ttu-id="d2b06-112">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d2b06-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d2b06-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2b06-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2b06-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2b06-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2b06-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2b06-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d2b06-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d2b06-117">tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="d2b06-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2b06-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2b06-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2b06-119">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d2b06-120">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d2b06-122">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d2b06-123">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="d2b06-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="d2b06-125">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d2b06-126">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="d2b06-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="d2b06-127">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="d2b06-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d2b06-128">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="d2b06-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d2b06-129">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="d2b06-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2b06-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2b06-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2b06-131">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="d2b06-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d2b06-132">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="d2b06-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d2b06-133">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d2b06-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d2b06-134">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="d2b06-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d2b06-135">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d2b06-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d2b06-136">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="d2b06-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d2b06-137">Wybierz opcję **tak**</span><span class="sxs-lookup"><span data-stu-id="d2b06-137">Select **Yes**</span></span>

  * <span data-ttu-id="d2b06-138">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="d2b06-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d2b06-139">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d2b06-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2b06-140">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d2b06-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d2b06-141">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-141">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d2b06-143">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  aplikacja sieci Web**aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d2b06-144">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d2b06-146">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="d2b06-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d2b06-147">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d2b06-148">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="d2b06-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="d2b06-149">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-149">Select **Next**.</span></span>

* <span data-ttu-id="d2b06-150">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d2b06-152">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d2b06-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2b06-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2b06-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d2b06-154">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d2b06-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d2b06-155">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="d2b06-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d2b06-156">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2b06-157">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2b06-158">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="d2b06-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d2b06-159">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2b06-160">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="d2b06-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d2b06-161">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="d2b06-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d2b06-163">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d2b06-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="d2b06-165">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="d2b06-165">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2b06-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2b06-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2b06-168">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="d2b06-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d2b06-169">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d2b06-170">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d2b06-171">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d2b06-172">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d2b06-173">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2b06-174">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="d2b06-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2b06-176">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d2b06-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d2b06-177">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="d2b06-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d2b06-178">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d2b06-179">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2b06-180">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2b06-181">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="d2b06-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d2b06-182">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d2b06-183">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="d2b06-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="d2b06-184">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="d2b06-184">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d2b06-186">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="d2b06-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d2b06-187">Dalej</span><span class="sxs-lookup"><span data-stu-id="d2b06-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d2b06-188">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d2b06-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d2b06-189">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="d2b06-190">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="d2b06-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2b06-191">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="d2b06-191">Create a web app.</span></span>
> * <span data-ttu-id="d2b06-192">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d2b06-193">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="d2b06-193">Work with a database.</span></span>
> * <span data-ttu-id="d2b06-194">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="d2b06-194">Add search and validation.</span></span>

<span data-ttu-id="d2b06-195">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d2b06-196">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d2b06-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2b06-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2b06-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2b06-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2b06-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2b06-199">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d2b06-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="d2b06-200">tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="d2b06-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2b06-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2b06-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2b06-202">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d2b06-203">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d2b06-205">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d2b06-206">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="d2b06-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="d2b06-208">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d2b06-209">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="d2b06-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="d2b06-210">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="d2b06-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d2b06-211">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="d2b06-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d2b06-212">Jest to podstawowy projekt początkowy i jest dobrym miejscem do rozpoczęcia.</span><span class="sxs-lookup"><span data-stu-id="d2b06-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2b06-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2b06-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2b06-214">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="d2b06-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d2b06-215">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="d2b06-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d2b06-216">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d2b06-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d2b06-217">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="d2b06-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d2b06-218">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d2b06-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d2b06-219">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="d2b06-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d2b06-220">Wybierz opcję **tak**</span><span class="sxs-lookup"><span data-stu-id="d2b06-220">Select **Yes**</span></span>

  * <span data-ttu-id="d2b06-221">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="d2b06-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d2b06-222">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d2b06-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2b06-223">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d2b06-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d2b06-224">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-224">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d2b06-226">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  aplikacja sieci Web**aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d2b06-227">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="d2b06-228">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="d2b06-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d2b06-229">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d2b06-230">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="d2b06-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="d2b06-231">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-231">Select **Next**.</span></span>

* <span data-ttu-id="d2b06-232">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d2b06-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="d2b06-233">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d2b06-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2b06-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2b06-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d2b06-235">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d2b06-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d2b06-236">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="d2b06-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d2b06-237">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2b06-238">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2b06-239">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="d2b06-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d2b06-240">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2b06-241">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="d2b06-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d2b06-242">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="d2b06-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d2b06-244">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d2b06-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="d2b06-246">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d2b06-247">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="d2b06-247">This app doesn't track personal information.</span></span> <span data-ttu-id="d2b06-248">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d2b06-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="d2b06-250">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="d2b06-250">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2b06-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2b06-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2b06-253">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="d2b06-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d2b06-254">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d2b06-255">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d2b06-256">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d2b06-257">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d2b06-258">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2b06-259">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="d2b06-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="d2b06-260">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d2b06-261">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="d2b06-261">This app doesn't track personal information.</span></span> <span data-ttu-id="d2b06-262">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d2b06-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="d2b06-264">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="d2b06-264">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2b06-266">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d2b06-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d2b06-267">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="d2b06-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d2b06-268">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d2b06-269">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="d2b06-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2b06-270">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="d2b06-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2b06-271">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="d2b06-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d2b06-272">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="d2b06-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d2b06-273">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="d2b06-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="d2b06-274">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="d2b06-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d2b06-275">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="d2b06-275">This app doesn't track personal information.</span></span> <span data-ttu-id="d2b06-276">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="d2b06-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="d2b06-278">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="d2b06-278">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d2b06-280">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="d2b06-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d2b06-281">Dalej</span><span class="sxs-lookup"><span data-stu-id="d2b06-281">Next</span></span>](adding-controller.md)

::: moniker-end
