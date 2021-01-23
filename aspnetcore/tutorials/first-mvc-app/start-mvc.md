---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710797"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f600d-103">Wprowadzenie do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f600d-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f600d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f600d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f600d-105">Jest to pierwszy samouczek serii, który uczy się ASP.NET Core tworzenia aplikacji sieci Web MVC z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="f600d-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="f600d-106">Na końcu serii będziesz mieć aplikację, która zarządza i wyświetla dane filmu.</span><span class="sxs-lookup"><span data-stu-id="f600d-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="f600d-107">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="f600d-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f600d-108">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="f600d-108">Create a web app.</span></span>
> * <span data-ttu-id="f600d-109">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="f600d-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f600d-110">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="f600d-110">Work with a database.</span></span>
> * <span data-ttu-id="f600d-111">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="f600d-111">Add search and validation.</span></span>

<span data-ttu-id="f600d-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f600d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f600d-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f600d-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f600d-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f600d-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f600d-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f600d-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f600d-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f600d-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f600d-117">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="f600d-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f600d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f600d-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f600d-119">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="f600d-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="f600d-120">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="f600d-121">W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `MvcMovie` w polu **Nazwa projektu**.</span><span class="sxs-lookup"><span data-stu-id="f600d-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="f600d-122">Ważne jest, aby nazwa projektu *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="f600d-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="f600d-123">Wielkie litery muszą pasować do każdego `namespace` dopasowania, gdy kod jest kopiowany.</span><span class="sxs-lookup"><span data-stu-id="f600d-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="f600d-124">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f600d-124">Select **Create**.</span></span>
* <span data-ttu-id="f600d-125">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:</span><span class="sxs-lookup"><span data-stu-id="f600d-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="f600d-126">**Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="f600d-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="f600d-127">**ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="f600d-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="f600d-128">**Create**.</span><span class="sxs-lookup"><span data-stu-id="f600d-128">**Create**.</span></span>

![<span data-ttu-id="f600d-129">Tworzenie nowej aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f600d-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="f600d-130">Aby zapoznać się z alternatywnymi metodami tworzenia projektu, zobacz [Tworzenie nowego projektu w programie Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="f600d-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="f600d-131">Program Visual Studio użył domyślnego szablonu projektu dla utworzonego projektu MVC.</span><span class="sxs-lookup"><span data-stu-id="f600d-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="f600d-132">Utworzony projekt:</span><span class="sxs-lookup"><span data-stu-id="f600d-132">The created project:</span></span>

* <span data-ttu-id="f600d-133">Jest działającą aplikacją.</span><span class="sxs-lookup"><span data-stu-id="f600d-133">Is a working app.</span></span>
* <span data-ttu-id="f600d-134">To podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="f600d-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f600d-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f600d-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f600d-136">W samouczku założono znajomość VS Code.</span><span class="sxs-lookup"><span data-stu-id="f600d-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="f600d-137">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="f600d-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="f600d-138">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f600d-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f600d-139">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="f600d-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="f600d-140">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f600d-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f600d-141">Jeśli zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do skompilowania i debugowania nie ma w "MvcMovie". Dodać je?**, wybierz pozycję **tak** .</span><span class="sxs-lookup"><span data-stu-id="f600d-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="f600d-142">`dotnet new mvc -o MvcMovie`: Tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="f600d-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f600d-143">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f600d-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f600d-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f600d-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f600d-145">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="f600d-145">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f600d-147">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="f600d-148">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="f600d-150">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="f600d-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f600d-151">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="f600d-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="f600d-152">Jeśli zostanie wyświetlona opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 5. x.</span><span class="sxs-lookup"><span data-stu-id="f600d-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="f600d-153">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-153">Select **Next**.</span></span>

* <span data-ttu-id="f600d-154">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f600d-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="f600d-156">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f600d-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f600d-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f600d-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f600d-158">Wybierz kombinację klawiszy CTRL + F5, aby uruchomić aplikację bez debugera.</span><span class="sxs-lookup"><span data-stu-id="f600d-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f600d-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f600d-159">Visual Studio:</span></span>

  * <span data-ttu-id="f600d-160">Uruchamia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="f600d-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="f600d-161">Uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="f600d-161">Runs the app.</span></span>

  <span data-ttu-id="f600d-162">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="f600d-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f600d-163">Standardowa nazwa hosta dla komputera lokalnego to `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f600d-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f600d-164">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="f600d-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="f600d-165">Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:</span><span class="sxs-lookup"><span data-stu-id="f600d-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f600d-166">Wprowadzanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-166">Make code changes.</span></span>
* <span data-ttu-id="f600d-167">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f600d-167">Save the file.</span></span>
* <span data-ttu-id="f600d-168">Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="f600d-169">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="f600d-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Debuguj](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="f600d-171">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f600d-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="f600d-173">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="f600d-173">The following image shows the app:</span></span>

![Strona główna lub indeks](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f600d-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f600d-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f600d-176">Wybierz kombinację klawiszy CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="f600d-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f600d-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="f600d-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="f600d-178">Uruchamia [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="f600d-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="f600d-179">Uruchamia przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="f600d-179">Launches a browser.</span></span>
  * <span data-ttu-id="f600d-180">Nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="f600d-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="f600d-181">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="f600d-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f600d-182">Standardowa nazwa hosta dla komputera lokalnego to `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f600d-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f600d-183">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="f600d-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="f600d-184">Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:</span><span class="sxs-lookup"><span data-stu-id="f600d-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f600d-185">Wprowadzanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-185">Make code changes.</span></span>
* <span data-ttu-id="f600d-186">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f600d-186">Save the file.</span></span>
* <span data-ttu-id="f600d-187">Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f600d-189">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f600d-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f600d-190">Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="f600d-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="f600d-191">Visual Studio dla komputerów Mac:</span><span class="sxs-lookup"><span data-stu-id="f600d-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="f600d-192">Uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) .</span><span class="sxs-lookup"><span data-stu-id="f600d-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="f600d-193">Uruchamia przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="f600d-193">Launches a browser.</span></span>
  * <span data-ttu-id="f600d-194">Nawiguje do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="f600d-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="f600d-195">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="f600d-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f600d-196">Standardowa nazwa hosta dla komputera lokalnego to `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f600d-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f600d-197">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="f600d-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="f600d-198">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="f600d-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="f600d-199">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="f600d-199">The following image shows the app:</span></span>

![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f600d-201">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="f600d-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f600d-202">Dalej: dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="f600d-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f600d-203">Jest to pierwszy samouczek serii, który uczy się ASP.NET Core tworzenia aplikacji sieci Web MVC z kontrolerami i widokami.</span><span class="sxs-lookup"><span data-stu-id="f600d-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="f600d-204">Na końcu serii będziesz mieć aplikację, która zarządza i wyświetla dane filmu.</span><span class="sxs-lookup"><span data-stu-id="f600d-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="f600d-205">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="f600d-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f600d-206">Utwórz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="f600d-206">Create a web app.</span></span>
> * <span data-ttu-id="f600d-207">Dodawanie i tworzenie szkieletu modelu.</span><span class="sxs-lookup"><span data-stu-id="f600d-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f600d-208">Pracuj z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="f600d-208">Work with a database.</span></span>
> * <span data-ttu-id="f600d-209">Dodaj wyszukiwanie i sprawdzanie poprawności.</span><span class="sxs-lookup"><span data-stu-id="f600d-209">Add search and validation.</span></span>

<span data-ttu-id="f600d-210">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f600d-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f600d-211">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f600d-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f600d-212">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f600d-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f600d-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f600d-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f600d-214">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f600d-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f600d-215">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="f600d-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f600d-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f600d-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f600d-217">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="f600d-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="f600d-218">Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Utwórz nowy projekt aplikacji sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f600d-220">Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f600d-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f600d-221">Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="f600d-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Skonfiguruj nowy projekt](start-mvc/_static/config.png)

* <span data-ttu-id="f600d-223">Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="f600d-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="f600d-224">Z listy rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,1**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f600d-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="f600d-225">Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="f600d-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="f600d-226">Program Visual Studio użył domyślnego szablonu projektu dla utworzonego projektu MVC.</span><span class="sxs-lookup"><span data-stu-id="f600d-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="f600d-227">Utworzony projekt:</span><span class="sxs-lookup"><span data-stu-id="f600d-227">The created project:</span></span>

* <span data-ttu-id="f600d-228">Jest działającą aplikacją.</span><span class="sxs-lookup"><span data-stu-id="f600d-228">Is a working app.</span></span>
* <span data-ttu-id="f600d-229">To podstawowy projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="f600d-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f600d-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f600d-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f600d-231">W samouczku założono znajomość VS Code.</span><span class="sxs-lookup"><span data-stu-id="f600d-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="f600d-232">Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="f600d-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="f600d-233">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f600d-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f600d-234">Zmień katalog ( `cd` ) do folderu, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="f600d-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="f600d-235">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f600d-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f600d-236">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="f600d-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="f600d-237">`dotnet new mvc -o MvcMovie`: Tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .</span><span class="sxs-lookup"><span data-stu-id="f600d-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f600d-238">`code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f600d-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f600d-239">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f600d-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f600d-240">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="f600d-240">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f600d-242">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="f600d-243">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="f600d-245">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="f600d-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f600d-246">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="f600d-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="f600d-247">Jeśli zostanie wyświetlona opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="f600d-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="f600d-248">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f600d-248">Select **Next**.</span></span>

* <span data-ttu-id="f600d-249">Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f600d-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="f600d-251">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f600d-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f600d-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f600d-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f600d-253">Wybierz kombinację klawiszy CTRL + F5, aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f600d-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f600d-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f600d-254">Visual Studio:</span></span>

  * <span data-ttu-id="f600d-255">Uruchamia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="f600d-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="f600d-256">Uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="f600d-256">Runs the app.</span></span>

  <span data-ttu-id="f600d-257">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="f600d-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f600d-258">Standardowa nazwa hosta dla komputera lokalnego to `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f600d-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f600d-259">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="f600d-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="f600d-260">Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:</span><span class="sxs-lookup"><span data-stu-id="f600d-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f600d-261">Wprowadzanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-261">Make code changes.</span></span>
* <span data-ttu-id="f600d-262">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f600d-262">Save the file.</span></span>
* <span data-ttu-id="f600d-263">Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="f600d-264">Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :</span><span class="sxs-lookup"><span data-stu-id="f600d-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menu Debuguj](start-mvc/_static/debug_menu.png)

<span data-ttu-id="f600d-266">Możesz debugować aplikację, wybierając przycisk **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f600d-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="f600d-268">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="f600d-268">The following image shows the app:</span></span>

![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f600d-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f600d-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f600d-271">Wybierz kombinację klawiszy CTRL + F5, aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f600d-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f600d-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="f600d-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="f600d-273">Uruchamia [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="f600d-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="f600d-274">Uruchamia przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="f600d-274">Launches a browser.</span></span>
  * <span data-ttu-id="f600d-275">Nawiguje do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="f600d-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="f600d-276">Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="f600d-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f600d-277">Standardowa nazwa hosta dla komputera lokalnego to `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f600d-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f600d-278">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="f600d-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="f600d-279">Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:</span><span class="sxs-lookup"><span data-stu-id="f600d-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f600d-280">Wprowadzanie zmian w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-280">Make code changes.</span></span>
* <span data-ttu-id="f600d-281">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="f600d-281">Save the file.</span></span>
* <span data-ttu-id="f600d-282">Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f600d-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f600d-284">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f600d-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f600d-285">Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="f600d-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="f600d-286">Visual Studio dla komputerów Mac: uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="f600d-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="f600d-287">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="f600d-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f600d-288">Standardowa nazwa hosta dla komputera lokalnego to `localhost` .</span><span class="sxs-lookup"><span data-stu-id="f600d-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f600d-289">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="f600d-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f600d-290">Po uruchomieniu aplikacji zobaczysz inny numer portu.</span><span class="sxs-lookup"><span data-stu-id="f600d-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="f600d-291">Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).</span><span class="sxs-lookup"><span data-stu-id="f600d-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="f600d-292">Na poniższej ilustracji przedstawiono aplikację:</span><span class="sxs-lookup"><span data-stu-id="f600d-292">The following image shows the app:</span></span>

![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f600d-294">W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.</span><span class="sxs-lookup"><span data-stu-id="f600d-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f600d-295">Dalej</span><span class="sxs-lookup"><span data-stu-id="f600d-295">Next</span></span>](adding-controller.md)

::: moniker-end
