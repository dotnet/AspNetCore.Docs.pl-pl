---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688532"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="57c9d-103">Wprowadzenie do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="57c9d-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="57c9d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="57c9d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57c9d-105">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="57c9d-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57c9d-106">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="57c9d-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="57c9d-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57c9d-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="57c9d-108">Create a web app.</span></span>
> * <span data-ttu-id="57c9d-109">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57c9d-110">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="57c9d-110">Work with a database.</span></span>
> * <span data-ttu-id="57c9d-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="57c9d-111">Add search and validation.</span></span>

<span data-ttu-id="57c9d-112">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="57c9d-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="57c9d-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="57c9d-117">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="57c9d-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="57c9d-119">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="57c9d-120">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="57c9d-121">W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `MvcMovie` w polu **Nazwa projektu**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="57c9d-122">Ważne jest, aby użyć tej dokładnej nazwy, z uwzględnieniem wielkich liter, więc każdy pasuje do tego, `namespace` kiedy kod jest kopiowany.</span><span class="sxs-lookup"><span data-stu-id="57c9d-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="57c9d-123">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-123">Select **Create**.</span></span>
1. <span data-ttu-id="57c9d-124">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:</span><span class="sxs-lookup"><span data-stu-id="57c9d-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="57c9d-125">**Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="57c9d-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="57c9d-126">**ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="57c9d-127">**Utwórz**</span><span class="sxs-lookup"><span data-stu-id="57c9d-127">**Create**</span></span>

![<span data-ttu-id="57c9d-128">Tworzenie nowej aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57c9d-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="57c9d-129">Aby zapoznać się z alternatywnymi metodami tworzenia projektu, zobacz [Tworzenie nowego projektu w programie Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="57c9d-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="57c9d-130">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="57c9d-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="57c9d-131">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="57c9d-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="57c9d-132">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="57c9d-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c9d-134">W samouczku założono znajomość VS Code.</span><span class="sxs-lookup"><span data-stu-id="57c9d-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="57c9d-135">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="57c9d-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="57c9d-136">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="57c9d-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57c9d-137">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="57c9d-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="57c9d-138">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="57c9d-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57c9d-139">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="57c9d-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="57c9d-140">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="57c9d-140">Select **Yes**</span></span>

  * <span data-ttu-id="57c9d-141">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="57c9d-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57c9d-142">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="57c9d-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-143">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57c9d-144">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-144">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57c9d-146">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57c9d-147">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="57c9d-149">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="57c9d-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57c9d-150">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57c9d-151">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, należy wybrać najnowszą wersję 5. x.</span><span class="sxs-lookup"><span data-stu-id="57c9d-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="57c9d-152">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-152">Select **Next**.</span></span>

* <span data-ttu-id="57c9d-153">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="57c9d-155">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="57c9d-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-156">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57c9d-157">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="57c9d-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="57c9d-158">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="57c9d-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57c9d-159">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-160">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c9d-161">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="57c9d-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="57c9d-162">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c9d-163">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="57c9d-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="57c9d-164">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="57c9d-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="57c9d-166">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="57c9d-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="57c9d-168">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="57c9d-168">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c9d-171">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="57c9d-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57c9d-172">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="57c9d-173">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-174">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57c9d-175">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="57c9d-176">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c9d-177">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="57c9d-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-179">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57c9d-180">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="57c9d-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="57c9d-181">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57c9d-182">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-183">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c9d-184">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="57c9d-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57c9d-185">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="57c9d-186">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="57c9d-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="57c9d-187">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="57c9d-187">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57c9d-189">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="57c9d-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57c9d-190">Dalej</span><span class="sxs-lookup"><span data-stu-id="57c9d-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57c9d-191">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="57c9d-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57c9d-192">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="57c9d-193">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="57c9d-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57c9d-194">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="57c9d-194">Create a web app.</span></span>
> * <span data-ttu-id="57c9d-195">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57c9d-196">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="57c9d-196">Work with a database.</span></span>
> * <span data-ttu-id="57c9d-197">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="57c9d-197">Add search and validation.</span></span>

<span data-ttu-id="57c9d-198">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="57c9d-199">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="57c9d-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-200">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-202">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="57c9d-203">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="57c9d-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-204">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57c9d-205">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="57c9d-206">Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="57c9d-208">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="57c9d-209">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="57c9d-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="57c9d-211">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="57c9d-212">Z listy rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,1**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="57c9d-213">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="57c9d-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="57c9d-214">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="57c9d-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="57c9d-215">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="57c9d-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="57c9d-216">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="57c9d-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c9d-218">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="57c9d-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="57c9d-219">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="57c9d-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="57c9d-220">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="57c9d-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57c9d-221">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="57c9d-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="57c9d-222">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="57c9d-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57c9d-223">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="57c9d-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="57c9d-224">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="57c9d-224">Select **Yes**</span></span>

  * <span data-ttu-id="57c9d-225">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="57c9d-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57c9d-226">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="57c9d-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-227">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57c9d-228">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-228">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57c9d-230">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57c9d-231">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="57c9d-233">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="57c9d-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57c9d-234">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57c9d-235">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="57c9d-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="57c9d-236">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-236">Select **Next**.</span></span>

* <span data-ttu-id="57c9d-237">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="57c9d-239">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="57c9d-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-240">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57c9d-241">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="57c9d-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="57c9d-242">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="57c9d-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57c9d-243">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-244">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c9d-245">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="57c9d-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="57c9d-246">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c9d-247">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="57c9d-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="57c9d-248">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="57c9d-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="57c9d-250">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="57c9d-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="57c9d-252">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="57c9d-252">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c9d-255">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="57c9d-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57c9d-256">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="57c9d-257">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-258">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57c9d-259">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="57c9d-260">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c9d-261">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="57c9d-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-263">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57c9d-264">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="57c9d-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="57c9d-265">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57c9d-266">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-267">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c9d-268">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="57c9d-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57c9d-269">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="57c9d-270">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="57c9d-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="57c9d-271">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="57c9d-271">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57c9d-273">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="57c9d-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57c9d-274">Dalej</span><span class="sxs-lookup"><span data-stu-id="57c9d-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="57c9d-275">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="57c9d-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57c9d-276">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="57c9d-277">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="57c9d-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57c9d-278">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="57c9d-278">Create a web app.</span></span>
> * <span data-ttu-id="57c9d-279">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="57c9d-280">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="57c9d-280">Work with a database.</span></span>
> * <span data-ttu-id="57c9d-281">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="57c9d-281">Add search and validation.</span></span>

<span data-ttu-id="57c9d-282">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="57c9d-283">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="57c9d-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-284">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-286">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="57c9d-287">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="57c9d-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-288">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57c9d-289">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="57c9d-290">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="57c9d-292">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="57c9d-293">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="57c9d-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="57c9d-295">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="57c9d-296">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="57c9d-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="57c9d-297">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="57c9d-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="57c9d-298">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="57c9d-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="57c9d-299">Jest to podstawowy projekt początkowy i jest dobrym miejscem do rozpoczęcia.</span><span class="sxs-lookup"><span data-stu-id="57c9d-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c9d-301">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="57c9d-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="57c9d-302">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="57c9d-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="57c9d-303">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="57c9d-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="57c9d-304">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="57c9d-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="57c9d-305">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="57c9d-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="57c9d-306">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="57c9d-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="57c9d-307">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="57c9d-307">Select **Yes**</span></span>

  * <span data-ttu-id="57c9d-308">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="57c9d-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="57c9d-309">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="57c9d-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-310">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57c9d-311">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-311">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57c9d-313">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="57c9d-314">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="57c9d-315">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="57c9d-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="57c9d-316">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="57c9d-317">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="57c9d-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="57c9d-318">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-318">Select **Next**.</span></span>

* <span data-ttu-id="57c9d-319">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="57c9d-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="57c9d-320">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="57c9d-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57c9d-321">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57c9d-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57c9d-322">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="57c9d-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="57c9d-323">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="57c9d-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57c9d-324">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-325">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c9d-326">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="57c9d-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="57c9d-327">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c9d-328">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="57c9d-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="57c9d-329">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="57c9d-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="57c9d-331">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="57c9d-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="57c9d-333">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="57c9d-334">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="57c9d-334">This app doesn't track personal information.</span></span> <span data-ttu-id="57c9d-335">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="57c9d-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="57c9d-337">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="57c9d-337">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="57c9d-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57c9d-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57c9d-340">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="57c9d-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="57c9d-341">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="57c9d-342">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-343">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57c9d-344">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="57c9d-345">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="57c9d-346">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="57c9d-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="57c9d-347">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="57c9d-348">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="57c9d-348">This app doesn't track personal information.</span></span> <span data-ttu-id="57c9d-349">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="57c9d-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="57c9d-351">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="57c9d-351">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57c9d-353">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="57c9d-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57c9d-354">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="57c9d-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="57c9d-355">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57c9d-356">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="57c9d-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57c9d-357">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="57c9d-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="57c9d-358">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="57c9d-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="57c9d-359">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="57c9d-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="57c9d-360">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="57c9d-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="57c9d-361">Wybierz pozycję **Zaakceptuj**, aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="57c9d-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="57c9d-362">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="57c9d-362">This app doesn't track personal information.</span></span> <span data-ttu-id="57c9d-363">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="57c9d-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="57c9d-365">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="57c9d-365">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="57c9d-367">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="57c9d-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57c9d-368">Dalej</span><span class="sxs-lookup"><span data-stu-id="57c9d-368">Next</span></span>](adding-controller.md)

::: moniker-end
