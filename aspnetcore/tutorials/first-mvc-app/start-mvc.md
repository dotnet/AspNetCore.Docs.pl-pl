---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422772"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="23dc8-103">Wprowadzenie do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="23dc8-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="23dc8-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23dc8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="23dc8-105">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="23dc8-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="23dc8-106">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="23dc8-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="23dc8-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23dc8-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="23dc8-108">Create a web app.</span></span>
> * <span data-ttu-id="23dc8-109">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="23dc8-110">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="23dc8-110">Work with a database.</span></span>
> * <span data-ttu-id="23dc8-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="23dc8-111">Add search and validation.</span></span>

<span data-ttu-id="23dc8-112">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="23dc8-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="23dc8-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="23dc8-117">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="23dc8-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="23dc8-119">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="23dc8-120">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="23dc8-121">W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `MvcMovie` w polu **Nazwa projektu**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="23dc8-122">Ważne jest, aby użyć tej dokładnej nazwy, z uwzględnieniem wielkich liter, więc każdy pasuje do tego, `namespace` kiedy kod jest kopiowany.</span><span class="sxs-lookup"><span data-stu-id="23dc8-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="23dc8-123">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-123">Select **Create**.</span></span>
1. <span data-ttu-id="23dc8-124">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:</span><span class="sxs-lookup"><span data-stu-id="23dc8-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="23dc8-125">**Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="23dc8-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="23dc8-126">**ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="23dc8-127">**Utwórz**</span><span class="sxs-lookup"><span data-stu-id="23dc8-127">**Create**</span></span>

![<span data-ttu-id="23dc8-128">Tworzenie nowej aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23dc8-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/5/mvc.png)

<span data-ttu-id="23dc8-129">Aby zapoznać się z alternatywnymi metodami tworzenia projektu, zobacz [Tworzenie nowego projektu w programie Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="23dc8-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="23dc8-130">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="23dc8-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="23dc8-131">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="23dc8-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="23dc8-132">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="23dc8-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23dc8-134">W samouczku założono znajomość VS Code.</span><span class="sxs-lookup"><span data-stu-id="23dc8-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="23dc8-135">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="23dc8-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="23dc8-136">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="23dc8-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="23dc8-137">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="23dc8-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="23dc8-138">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="23dc8-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="23dc8-139">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="23dc8-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="23dc8-140">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="23dc8-140">Select **Yes**</span></span>

  * <span data-ttu-id="23dc8-141">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="23dc8-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="23dc8-142">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="23dc8-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-143">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23dc8-144">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-144">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="23dc8-146">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="23dc8-147">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="23dc8-149">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="23dc8-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="23dc8-150">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="23dc8-151">Jeśli jest prezentowana opcja wyboru **platformy docelowej** , należy wybrać najnowszą wersję 5. x.</span><span class="sxs-lookup"><span data-stu-id="23dc8-151">If presented an option to select a **Target Framework** , select the latest 5.x version.</span></span>

  <span data-ttu-id="23dc8-152">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-152">Select **Next**.</span></span>

* <span data-ttu-id="23dc8-153">Nazwij projekt **MvcMovie** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-153">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="23dc8-155">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="23dc8-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-156">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="23dc8-157">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="23dc8-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="23dc8-158">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="23dc8-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="23dc8-159">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-160">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="23dc8-161">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="23dc8-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="23dc8-162">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="23dc8-163">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="23dc8-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="23dc8-164">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="23dc8-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="23dc8-166">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="23dc8-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="23dc8-168">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="23dc8-168">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23dc8-171">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="23dc8-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="23dc8-172">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="23dc8-173">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-174">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="23dc8-175">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="23dc8-176">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="23dc8-177">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="23dc8-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-179">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="23dc8-180">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23dc8-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="23dc8-181">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="23dc8-182">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-183">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="23dc8-184">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="23dc8-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="23dc8-185">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="23dc8-186">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="23dc8-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="23dc8-187">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="23dc8-187">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="23dc8-189">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="23dc8-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="23dc8-190">Dalej</span><span class="sxs-lookup"><span data-stu-id="23dc8-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="23dc8-191">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="23dc8-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="23dc8-192">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="23dc8-193">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="23dc8-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23dc8-194">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="23dc8-194">Create a web app.</span></span>
> * <span data-ttu-id="23dc8-195">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="23dc8-196">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="23dc8-196">Work with a database.</span></span>
> * <span data-ttu-id="23dc8-197">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="23dc8-197">Add search and validation.</span></span>

<span data-ttu-id="23dc8-198">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="23dc8-199">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="23dc8-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-200">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-202">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="23dc8-203">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="23dc8-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-204">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23dc8-205">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="23dc8-206">Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="23dc8-208">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="23dc8-209">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="23dc8-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="23dc8-211">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="23dc8-212">Z listy rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,1** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="23dc8-213">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="23dc8-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="23dc8-214">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="23dc8-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="23dc8-215">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="23dc8-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="23dc8-216">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="23dc8-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23dc8-218">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="23dc8-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="23dc8-219">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="23dc8-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="23dc8-220">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="23dc8-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="23dc8-221">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="23dc8-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="23dc8-222">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="23dc8-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="23dc8-223">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="23dc8-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="23dc8-224">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="23dc8-224">Select **Yes**</span></span>

  * <span data-ttu-id="23dc8-225">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="23dc8-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="23dc8-226">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="23dc8-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-227">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23dc8-228">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-228">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="23dc8-230">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="23dc8-231">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="23dc8-233">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="23dc8-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="23dc8-234">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="23dc8-235">Jeśli jest prezentowana opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="23dc8-235">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="23dc8-236">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-236">Select **Next**.</span></span>

* <span data-ttu-id="23dc8-237">Nazwij projekt **MvcMovie** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-237">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="23dc8-239">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="23dc8-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-240">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="23dc8-241">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="23dc8-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="23dc8-242">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="23dc8-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="23dc8-243">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-244">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="23dc8-245">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="23dc8-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="23dc8-246">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="23dc8-247">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="23dc8-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="23dc8-248">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="23dc8-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="23dc8-250">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="23dc8-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="23dc8-252">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="23dc8-252">The following image shows the app:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23dc8-255">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="23dc8-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="23dc8-256">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="23dc8-257">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-258">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="23dc8-259">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="23dc8-260">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="23dc8-261">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="23dc8-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-263">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="23dc8-264">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23dc8-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="23dc8-265">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="23dc8-266">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-267">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="23dc8-268">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="23dc8-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="23dc8-269">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="23dc8-270">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="23dc8-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="23dc8-271">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="23dc8-271">The following image shows the app:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="23dc8-273">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="23dc8-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="23dc8-274">Dalej</span><span class="sxs-lookup"><span data-stu-id="23dc8-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="23dc8-275">Ten samouczek uczy się podstaw tworzenia aplikacji sieci Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="23dc8-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="23dc8-276">Aplikacja zarządza bazą danych tytułów filmu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="23dc8-277">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="23dc8-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23dc8-278">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="23dc8-278">Create a web app.</span></span>
> * <span data-ttu-id="23dc8-279">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="23dc8-280">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="23dc8-280">Work with a database.</span></span>
> * <span data-ttu-id="23dc8-281">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="23dc8-281">Add search and validation.</span></span>

<span data-ttu-id="23dc8-282">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="23dc8-283">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="23dc8-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-284">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-286">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="23dc8-287">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="23dc8-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-288">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="23dc8-289">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="23dc8-290">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="23dc8-292">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="23dc8-293">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="23dc8-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="23dc8-295">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-295">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="23dc8-296">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="23dc8-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="23dc8-297">Program Visual Studio użył szablonu domyślnego dla projektu MVC, który właśnie został utworzony.</span><span class="sxs-lookup"><span data-stu-id="23dc8-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="23dc8-298">Teraz masz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="23dc8-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="23dc8-299">Jest to podstawowy projekt początkowy i jest dobrym miejscem do rozpoczęcia.</span><span class="sxs-lookup"><span data-stu-id="23dc8-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23dc8-301">Samouczek założono familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="23dc8-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="23dc8-302">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="23dc8-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="23dc8-303">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="23dc8-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="23dc8-304">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="23dc8-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="23dc8-305">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="23dc8-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="23dc8-306">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="23dc8-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="23dc8-307">Wybierz pozycję **Tak**</span><span class="sxs-lookup"><span data-stu-id="23dc8-307">Select **Yes**</span></span>

  * <span data-ttu-id="23dc8-308">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="23dc8-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="23dc8-309">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="23dc8-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-310">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="23dc8-311">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-311">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="23dc8-313">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="23dc8-314">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >  **App**  >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="23dc8-315">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="23dc8-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="23dc8-316">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="23dc8-317">Jeśli jest prezentowana opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="23dc8-317">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="23dc8-318">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-318">Select **Next**.</span></span>

* <span data-ttu-id="23dc8-319">Nazwij projekt **MvcMovie** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="23dc8-319">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="23dc8-320">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="23dc8-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="23dc8-321">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23dc8-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="23dc8-322">Wybierz **kombinację klawiszy CTRL-F5** , aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="23dc8-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="23dc8-323">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="23dc8-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="23dc8-324">Zauważ, że pasek adresu pokazuje, `localhost:port#` a nie coś innego `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-325">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="23dc8-326">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="23dc8-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="23dc8-327">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="23dc8-328">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="23dc8-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="23dc8-329">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="23dc8-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Debuguj](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="23dc8-331">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="23dc8-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="23dc8-333">Wybierz pozycję **Zaakceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="23dc8-334">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="23dc8-334">This app doesn't track personal information.</span></span> <span data-ttu-id="23dc8-335">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="23dc8-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="23dc8-337">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="23dc8-337">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="23dc8-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23dc8-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="23dc8-340">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="23dc8-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="23dc8-341">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="23dc8-342">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-343">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="23dc8-344">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="23dc8-345">Uruchamianie aplikacji za pomocą klawiszy CTRL + F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="23dc8-346">Wielu deweloperów woli używać trybu niedebugowania do odświeżania strony i wyświetlania zmian.</span><span class="sxs-lookup"><span data-stu-id="23dc8-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="23dc8-347">Wybierz pozycję **Zaakceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="23dc8-348">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="23dc8-348">This app doesn't track personal information.</span></span> <span data-ttu-id="23dc8-349">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="23dc8-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="23dc8-351">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="23dc8-351">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="23dc8-353">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="23dc8-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="23dc8-354">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="23dc8-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="23dc8-355">Visual Studio dla komputerów Mac uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="23dc8-356">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="23dc8-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="23dc8-357">To dlatego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="23dc8-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="23dc8-358">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="23dc8-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="23dc8-359">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="23dc8-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="23dc8-360">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="23dc8-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="23dc8-361">Wybierz pozycję **Zaakceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="23dc8-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="23dc8-362">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="23dc8-362">This app doesn't track personal information.</span></span> <span data-ttu-id="23dc8-363">Kod wygenerowany przez szablon zawiera zasoby, które pomagają spełnić [ogólne rozporządzenie o ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="23dc8-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="23dc8-365">Na poniższej ilustracji przedstawiono aplikację po przyjęciu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="23dc8-365">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="23dc8-367">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="23dc8-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="23dc8-368">Dalej</span><span class="sxs-lookup"><span data-stu-id="23dc8-368">Next</span></span>](adding-controller.md)

::: moniker-end
