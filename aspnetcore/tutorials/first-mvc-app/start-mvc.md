---
title: Rozpoczęcie pracy z ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662478"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="0522e-103">Rozpoczęcie pracy z ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0522e-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="0522e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0522e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="0522e-105">Ten samouczek uczy podstaw tworzenia aplikacji sieci web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0522e-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="0522e-106">Aplikacja zarządza bazą danych tytułów filmów.</span><span class="sxs-lookup"><span data-stu-id="0522e-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="0522e-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="0522e-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0522e-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="0522e-108">Create a web app.</span></span>
> * <span data-ttu-id="0522e-109">Dodaj i rusztowanie modelu.</span><span class="sxs-lookup"><span data-stu-id="0522e-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="0522e-110">Praca z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="0522e-110">Work with a database.</span></span>
> * <span data-ttu-id="0522e-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="0522e-111">Add search and validation.</span></span>

<span data-ttu-id="0522e-112">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="0522e-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="0522e-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0522e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0522e-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0522e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0522e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0522e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0522e-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0522e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="0522e-117">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="0522e-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0522e-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0522e-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0522e-119">Z programu Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="0522e-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="0522e-120">Wybierz **ASP.NET Podstawową aplikację sieci Web,** a następnie wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="0522e-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="0522e-122">Nazwij projekt **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0522e-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="0522e-123">Ważne jest, aby nadać nazwę projektowi **MvcMovie,** więc podczas kopiowania kodu obszar nazw będzie zgodny.</span><span class="sxs-lookup"><span data-stu-id="0522e-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/config.png)

* <span data-ttu-id="0522e-125">Wybierz **pozycję Aplikacja sieci Web(Kontroler widoku modelu),** a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0522e-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="0522e-126">Nowe okno dialogowe projektu ,NET Core w lewym okienku, ASP.NET sieć Web Core</span><span class="sxs-lookup"><span data-stu-id="0522e-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="0522e-127">Program Visual Studio użył domyślnego szablonu dla właśnie utworzonego projektu MVC.</span><span class="sxs-lookup"><span data-stu-id="0522e-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="0522e-128">Masz teraz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="0522e-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="0522e-129">Jest to podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="0522e-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0522e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0522e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0522e-131">Samouczek zakłada familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="0522e-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="0522e-132">Aby uzyskać więcej [informacji,](https://code.visualstudio.com/docs) zobacz Wprowadzenie do programu VS Code i visual [studio code.](#visual-studio-code-help)</span><span class="sxs-lookup"><span data-stu-id="0522e-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="0522e-133">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0522e-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0522e-134">Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="0522e-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="0522e-135">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0522e-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="0522e-136">W oknie dialogowym z **wymaganymi zasobami do tworzenia i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="0522e-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="0522e-137">Wybierz **tak**</span><span class="sxs-lookup"><span data-stu-id="0522e-137">Select **Yes**</span></span>

  * <span data-ttu-id="0522e-138">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie.*</span><span class="sxs-lookup"><span data-stu-id="0522e-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="0522e-139">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie.csproj* w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0522e-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0522e-140">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0522e-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0522e-141">Wybierz **pozycję Plik** > **nowego rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="0522e-141">Select **File** > **New Solution**.</span></span>

  ![macOS Nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0522e-143">Wybierz opcję **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="0522e-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Nowe okno dialogowe projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="0522e-145">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** ustaw **docelową platformę** **programu .NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="0522e-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![Wybór systemu macOS .NET Core 3.1](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="0522e-147">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0522e-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="0522e-148">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0522e-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0522e-149">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0522e-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0522e-150">Wybierz **klawisz Ctrl-F5,** aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="0522e-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="0522e-151">Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="0522e-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0522e-152">Zwróć uwagę, że `localhost:port#` pasek adresu `example.com`pokazuje, a nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="0522e-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0522e-153">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="0522e-154">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="0522e-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="0522e-155">Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0522e-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="0522e-156">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="0522e-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="0522e-157">Aplikację można uruchomić w trybie debugowania lub bez debugowania z elementu menu **Debugowania:**</span><span class="sxs-lookup"><span data-stu-id="0522e-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu debugowania](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="0522e-159">Aplikację można debugować, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0522e-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="0522e-161">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="0522e-161">The following image shows the app:</span></span>

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0522e-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0522e-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0522e-164">Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="0522e-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="0522e-165">Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `https://localhost:5001`przeglądarkę i przechodzi do .</span><span class="sxs-lookup"><span data-stu-id="0522e-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="0522e-166">Pasek adresu `localhost:port:5001` pokazuje, a `example.com`nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="0522e-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="0522e-167">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0522e-168">Localhost obsługuje tylko żądania sieci web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="0522e-169">Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0522e-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="0522e-170">Wielu deweloperów woli używać trybu bez debugowania, aby odświeżyć stronę i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="0522e-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0522e-172">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0522e-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0522e-173">Wybierz **uruchom** > **start bez debugowania,** aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="0522e-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="0522e-174">Program Visual Studio dla komputerów Mac uruchamia serwer [Kestrel,](xref:fundamentals/servers/index#kestrel) uruchamia przeglądarkę i przechodzi do `http://localhost:port`, gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="0522e-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0522e-175">Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="0522e-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0522e-176">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="0522e-177">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="0522e-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="0522e-178">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="0522e-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="0522e-179">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Uruchom.**</span><span class="sxs-lookup"><span data-stu-id="0522e-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="0522e-180">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="0522e-180">The following image shows the app:</span></span>

  ![Strona główna lub Indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="0522e-182">W następnej części tego samouczka dowiesz się o MVC i rozpocząć pisanie kodu.</span><span class="sxs-lookup"><span data-stu-id="0522e-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0522e-183">Dalej</span><span class="sxs-lookup"><span data-stu-id="0522e-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="0522e-184">Ten samouczek uczy podstaw tworzenia aplikacji sieci web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0522e-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="0522e-185">Aplikacja zarządza bazą danych tytułów filmów.</span><span class="sxs-lookup"><span data-stu-id="0522e-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="0522e-186">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="0522e-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0522e-187">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="0522e-187">Create a web app.</span></span>
> * <span data-ttu-id="0522e-188">Dodaj i rusztowanie modelu.</span><span class="sxs-lookup"><span data-stu-id="0522e-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="0522e-189">Praca z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="0522e-189">Work with a database.</span></span>
> * <span data-ttu-id="0522e-190">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="0522e-190">Add search and validation.</span></span>

<span data-ttu-id="0522e-191">Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.</span><span class="sxs-lookup"><span data-stu-id="0522e-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="0522e-192">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0522e-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0522e-193">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0522e-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0522e-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0522e-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0522e-195">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0522e-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="0522e-196">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="0522e-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0522e-197">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0522e-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0522e-198">Z programu Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="0522e-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="0522e-199">Wybierz **ASP.NET Podstawową aplikację sieci Web,** a następnie wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="0522e-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="0522e-201">Nazwij projekt **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0522e-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="0522e-202">Ważne jest, aby nadać nazwę projektowi **MvcMovie,** więc podczas kopiowania kodu obszar nazw będzie zgodny.</span><span class="sxs-lookup"><span data-stu-id="0522e-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/config.png)


* <span data-ttu-id="0522e-204">Wybierz **pozycję Aplikacja sieci Web(Kontroler widoku modelu),** a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0522e-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="0522e-205">Nowe okno dialogowe projektu ,NET Core w lewym okienku, ASP.NET sieć Web Core</span><span class="sxs-lookup"><span data-stu-id="0522e-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="0522e-206">Program Visual Studio użył domyślnego szablonu dla właśnie utworzonego projektu MVC.</span><span class="sxs-lookup"><span data-stu-id="0522e-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="0522e-207">Masz teraz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji.</span><span class="sxs-lookup"><span data-stu-id="0522e-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="0522e-208">Jest to podstawowy projekt startowy i jest to dobre miejsce do rozpoczęcia.</span><span class="sxs-lookup"><span data-stu-id="0522e-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0522e-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0522e-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0522e-210">Samouczek zakłada familarity z VS Code.</span><span class="sxs-lookup"><span data-stu-id="0522e-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="0522e-211">Aby uzyskać więcej [informacji,](https://code.visualstudio.com/docs) zobacz Wprowadzenie do programu VS Code i visual [studio code.](#visual-studio-code-help)</span><span class="sxs-lookup"><span data-stu-id="0522e-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="0522e-212">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0522e-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0522e-213">Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="0522e-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="0522e-214">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0522e-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="0522e-215">W oknie dialogowym z **wymaganymi zasobami do tworzenia i debugowania brakuje w "MvcMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="0522e-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="0522e-216">Wybierz **tak**</span><span class="sxs-lookup"><span data-stu-id="0522e-216">Select **Yes**</span></span>

  * <span data-ttu-id="0522e-217">`dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie.*</span><span class="sxs-lookup"><span data-stu-id="0522e-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="0522e-218">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie.csproj* w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0522e-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0522e-219">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0522e-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0522e-220">Wybierz **pozycję Plik** > **nowego rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="0522e-220">Select **File** > **New Solution**.</span></span>

  ![macOS Nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0522e-222">Wybierz opcję **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="0522e-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Nowe okno dialogowe projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="0522e-224">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** zaakceptuj domyślną **platformę docelową** **programu .NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="0522e-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Wybór systemu macOS .NET Core 2.2](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="0522e-226">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0522e-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="0522e-227">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0522e-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0522e-228">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0522e-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0522e-229">Wybierz **klawisz Ctrl-F5,** aby uruchomić aplikację w trybie bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="0522e-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="0522e-230">Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="0522e-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0522e-231">Zwróć uwagę, że `localhost:port#` pasek adresu `example.com`pokazuje, a nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="0522e-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0522e-232">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="0522e-233">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="0522e-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="0522e-234">Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0522e-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="0522e-235">Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="0522e-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="0522e-236">Aplikację można uruchomić w trybie debugowania lub bez debugowania z elementu menu **Debugowania:**</span><span class="sxs-lookup"><span data-stu-id="0522e-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu debugowania](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="0522e-238">Aplikację można debugować, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0522e-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="0522e-240">Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="0522e-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="0522e-241">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="0522e-241">This app doesn't track personal information.</span></span> <span data-ttu-id="0522e-242">Wygenerowany szablon kod zawiera zasoby ułatwiające spełnienie [ogólnego rozporządzenia o ochronie danych (RODO).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="0522e-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub Indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="0522e-244">Na poniższej ilustracji przedstawiono aplikację po zaakceptowaniu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="0522e-244">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0522e-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0522e-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0522e-247">Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="0522e-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="0522e-248">Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `https://localhost:5001`przeglądarkę i przechodzi do .</span><span class="sxs-lookup"><span data-stu-id="0522e-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="0522e-249">Pasek adresu `localhost:port:5001` pokazuje, a `example.com`nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="0522e-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="0522e-250">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0522e-251">Localhost obsługuje tylko żądania sieci web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="0522e-252">Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0522e-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="0522e-253">Wielu deweloperów woli używać trybu bez debugowania, aby odświeżyć stronę i wyświetlić zmiany.</span><span class="sxs-lookup"><span data-stu-id="0522e-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="0522e-254">Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="0522e-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="0522e-255">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="0522e-255">This app doesn't track personal information.</span></span> <span data-ttu-id="0522e-256">Wygenerowany szablon kod zawiera zasoby ułatwiające spełnienie [ogólnego rozporządzenia o ochronie danych (RODO).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="0522e-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub Indeks](start-mvc/_static/privacy.png)

  <span data-ttu-id="0522e-258">Na poniższej ilustracji przedstawiono aplikację po zaakceptowaniu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="0522e-258">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0522e-260">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0522e-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0522e-261">Wybierz **uruchom** > **start bez debugowania,** aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="0522e-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="0522e-262">Program Visual Studio dla komputerów Mac uruchamia serwer [Kestrel,](xref:fundamentals/servers/index#kestrel) uruchamia przeglądarkę i przechodzi do `http://localhost:port`, gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="0522e-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0522e-263">Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="0522e-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0522e-264">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="0522e-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="0522e-265">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="0522e-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="0522e-266">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="0522e-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="0522e-267">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Uruchom.**</span><span class="sxs-lookup"><span data-stu-id="0522e-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="0522e-268">Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="0522e-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="0522e-269">Ta aplikacja nie śledzi informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="0522e-269">This app doesn't track personal information.</span></span> <span data-ttu-id="0522e-270">Wygenerowany szablon kod zawiera zasoby ułatwiające spełnienie [ogólnego rozporządzenia o ochronie danych (RODO).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="0522e-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub Indeks](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="0522e-272">Na poniższej ilustracji przedstawiono aplikację po zaakceptowaniu śledzenia:</span><span class="sxs-lookup"><span data-stu-id="0522e-272">The following image shows the app after accepting tracking:</span></span>

  ![Strona główna lub Indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="0522e-274">W następnej części tego samouczka dowiesz się o MVC i rozpocząć pisanie kodu.</span><span class="sxs-lookup"><span data-stu-id="0522e-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0522e-275">Dalej</span><span class="sxs-lookup"><span data-stu-id="0522e-275">Next</span></span>](adding-controller.md)

::: moniker-end
