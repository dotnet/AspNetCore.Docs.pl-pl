---
title: 'Samouczek: Wprowadzenie do stron Razor w ASP.NET Core'
author: rick-anderson
description: Ta seria samouczków pokazuje, jak używać stron Razor w ASP.NET Core. Dowiedz się, jak utworzyć model, wygenerować kod dla stron Razor, użyć Entity Framework Core i SQL Server do dostępu do danych, dodać funkcje wyszukiwania, dodać sprawdzanie poprawności danych i użyć migracji, aby zaktualizować model.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658544"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="81279-104">Samouczek: Wprowadzenie do stron Razor w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81279-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="81279-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81279-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="81279-106">Jest to pierwszy samouczek z serii, który uczy podstaw tworzenia aplikacji sieci web ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="81279-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="81279-107">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="81279-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="81279-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="81279-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="81279-109">Tworzenie aplikacji internetowej Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="81279-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="81279-110">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="81279-110">Run the app.</span></span>
> * <span data-ttu-id="81279-111">Sprawdź pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="81279-111">Examine the project files.</span></span>

<span data-ttu-id="81279-112">Na końcu tego samouczka będziesz mieć działającą aplikację internetową Razor Pages, którą będziesz przeć w późniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="81279-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="81279-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="81279-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81279-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81279-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="81279-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81279-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81279-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="81279-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="81279-118">Tworzenie aplikacji internetowych ze stronami Razor</span><span class="sxs-lookup"><span data-stu-id="81279-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81279-119">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81279-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81279-120">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="81279-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="81279-121">Utwórz nową ASP.NET podstawową aplikację sieci Web i wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="81279-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="81279-122">![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="81279-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="81279-123">Nazwij projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="81279-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="81279-124">Ważne jest, aby nadać projektowi *RazorPagesMovie,* aby obszary nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="81279-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="81279-125">![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="81279-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="81279-126">Wybierz **ASP.NET Core 3.1** w menu **rozwijanym, Aplikacja sieci Web**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="81279-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="81279-128">Tworzony jest następujący projekt startowy:</span><span class="sxs-lookup"><span data-stu-id="81279-128">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="81279-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81279-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="81279-131">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="81279-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="81279-132">Zmień katalog (`cd`), który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="81279-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="81279-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="81279-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="81279-134">Polecenie `dotnet new` tworzy nowy projekt Razor Pages w folderze *RazorPagesMovie.*</span><span class="sxs-lookup"><span data-stu-id="81279-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="81279-135">Polecenie `code` otwiera folder *RazorPagesMovie* w bieżącym wystąpieniu programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="81279-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="81279-136">Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do kompilacji i debugowania brakuje "RazorPagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="81279-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="81279-137">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="81279-137">Select **Yes**.</span></span>

  <span data-ttu-id="81279-138">Katalog *.vscode* zawierający pliki *launch.json* i *tasks.json* jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="81279-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81279-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="81279-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="81279-140">Wybierz **pozycję Plik** > **nowego rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="81279-140">Select **File** > **New Solution**.</span></span>

![macOS Nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="81279-142">Wybierz pozycję **.NET Core** > **App** > **Web Application** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="81279-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Nowe okno dialogowe projektu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="81279-144">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** ustaw **platformę docelową** na **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="81279-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Wybór systemu macOS .NET Core 3.1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="81279-146">Nazwij projekt **RazorPagesMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="81279-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nazwaproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="81279-148">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="81279-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="81279-149">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="81279-149">Examine the project files</span></span>

<span data-ttu-id="81279-150">Oto omówienie głównych folderów projektu i plików, z którymi będziesz pracować w późniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="81279-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="81279-151">Folder Strony</span><span class="sxs-lookup"><span data-stu-id="81279-151">Pages folder</span></span>

<span data-ttu-id="81279-152">Zawiera strony Razor i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="81279-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="81279-153">Każda strona Razor to para plików:</span><span class="sxs-lookup"><span data-stu-id="81279-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="81279-154">Plik *cshtml zawierający* znaczniki HTML z kodem C# przy użyciu składni Razor.</span><span class="sxs-lookup"><span data-stu-id="81279-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="81279-155">Plik *.cshtml.cs* zawierający kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="81279-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="81279-156">Pliki pomocnicze mają nazwy, które zaczynają się od podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="81279-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="81279-157">Na przykład plik *_Layout.cshtml* konfiguruje elementy interfejsu użytkownika wspólne dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="81279-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="81279-158">Ten plik konfiguruje menu nawigacyjne w górnej części strony i informacje o prawach autorskich u dołu strony.</span><span class="sxs-lookup"><span data-stu-id="81279-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="81279-159">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="81279-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="81279-160">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="81279-160">wwwroot folder</span></span>

<span data-ttu-id="81279-161">Zawiera pliki statyczne, takie jak pliki HTML, Pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="81279-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="81279-162">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="81279-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="81279-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="81279-163">appSettings.json</span></span>

<span data-ttu-id="81279-164">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="81279-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="81279-165">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="81279-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="81279-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="81279-166">Program.cs</span></span>

<span data-ttu-id="81279-167">Zawiera punkt wejścia programu.</span><span class="sxs-lookup"><span data-stu-id="81279-167">Contains the entry point for the program.</span></span> <span data-ttu-id="81279-168">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="81279-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="81279-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="81279-169">Startup.cs</span></span>

<span data-ttu-id="81279-170">Zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="81279-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="81279-171">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="81279-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="81279-172">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="81279-172">Next steps</span></span>

<span data-ttu-id="81279-173">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="81279-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="81279-174">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="81279-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81279-175">Jest to pierwszy samouczek z serii.</span><span class="sxs-lookup"><span data-stu-id="81279-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="81279-176">[Seria](xref:tutorials/razor-pages/index) uczy podstaw budowania aplikacji internetowej ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="81279-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="81279-177">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="81279-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="81279-178">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="81279-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="81279-179">Tworzenie aplikacji internetowej Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="81279-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="81279-180">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="81279-180">Run the app.</span></span>
> * <span data-ttu-id="81279-181">Sprawdź pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="81279-181">Examine the project files.</span></span>

<span data-ttu-id="81279-182">Na końcu tego samouczka będziesz mieć działającą aplikację internetową Razor Pages, którą będziesz przeć w późniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="81279-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="81279-184">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="81279-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81279-185">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81279-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="81279-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81279-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81279-187">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="81279-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="81279-188">Tworzenie aplikacji internetowych ze stronami Razor</span><span class="sxs-lookup"><span data-stu-id="81279-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81279-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81279-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81279-190">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="81279-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="81279-191">Utwórz nową ASP.NET podstawową aplikację sieci Web i wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="81279-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="81279-193">Nazwij projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="81279-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="81279-194">Ważne jest, aby nadać projektowi *RazorPagesMovie,* aby obszary nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="81279-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/config.png)

* <span data-ttu-id="81279-196">Wybierz **ASP.NET Core 2.2** w menu **rozwijanym, Aplikacja sieci Web**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="81279-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="81279-198">Tworzony jest następujący projekt startowy:</span><span class="sxs-lookup"><span data-stu-id="81279-198">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="81279-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81279-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="81279-201">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="81279-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="81279-202">Zmień katalog (`cd`), który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="81279-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="81279-203">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="81279-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="81279-204">Polecenie `dotnet new` tworzy nowy projekt Razor Pages w folderze *RazorPagesMovie.*</span><span class="sxs-lookup"><span data-stu-id="81279-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="81279-205">Polecenie `code` otwiera folder *RazorPagesMovie* w bieżącym wystąpieniu programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="81279-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="81279-206">Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do kompilacji i debugowania brakuje "RazorPagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="81279-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="81279-207">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="81279-207">Select **Yes**.</span></span>

  <span data-ttu-id="81279-208">Katalog *.vscode* zawierający pliki *launch.json* i *tasks.json* jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="81279-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81279-209">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="81279-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="81279-210">Wybierz **pozycję Plik** > **nowego rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="81279-210">Select **File** > **New Solution**.</span></span>

![macOS Nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="81279-212">Wybierz pozycję **.NET Core** > **App** > **Web Application** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="81279-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Nowe okno dialogowe projektu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="81279-214">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** ustaw **platformę docelową** na **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="81279-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Wybór systemu macOS .NET Core 3.0](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="81279-216">Nazwij projekt **RazorPagesMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="81279-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nazwaproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="81279-218">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="81279-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81279-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81279-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81279-220">Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="81279-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="81279-221">Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="81279-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="81279-222">Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="81279-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="81279-223">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="81279-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="81279-224">Localhost obsługuje tylko żądania sieci web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="81279-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="81279-225">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="81279-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="81279-226">Na stronie głównej aplikacji wybierz pozycję **Zaakceptuj,** aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="81279-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="81279-227">Ta aplikacja nie śledzi danych osobowych, ale szablon projektu zawiera funkcję zgody w przypadku, gdy jest to potrzebne do przestrzegania ogólnego rozporządzenia o [ochronie danych (RODO)](xref:security/gdpr)Unii Europejskiej.</span><span class="sxs-lookup"><span data-stu-id="81279-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub Indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="81279-229">Na poniższej ilustracji przedstawiono aplikację po wyrażeniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="81279-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="81279-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81279-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="81279-232">Naciśnij **klawisze Ctrl-F5,** aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="81279-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="81279-233">Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `http://localhost:5001`przeglądarkę i przechodzi do .</span><span class="sxs-lookup"><span data-stu-id="81279-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="81279-234">Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu .</span><span class="sxs-lookup"><span data-stu-id="81279-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="81279-235">To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="81279-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="81279-236">Localhost obsługuje tylko żądania sieci web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="81279-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="81279-237">Na stronie głównej aplikacji wybierz pozycję **Zaakceptuj,** aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="81279-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="81279-238">Ta aplikacja nie śledzi danych osobowych, ale szablon projektu zawiera funkcję zgody w przypadku, gdy jest to potrzebne do przestrzegania ogólnego rozporządzenia o [ochronie danych (RODO)](xref:security/gdpr)Unii Europejskiej.</span><span class="sxs-lookup"><span data-stu-id="81279-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub Indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="81279-240">Na poniższej ilustracji przedstawiono aplikację po wyrażeniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="81279-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81279-242">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="81279-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="81279-243">Naciśnij **cmd-opt-F5,** aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="81279-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="81279-244">Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `http://localhost:5001`przeglądarkę i przechodzi do .</span><span class="sxs-lookup"><span data-stu-id="81279-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="81279-245">Na stronie głównej aplikacji wybierz pozycję **Zaakceptuj,** aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="81279-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="81279-246">Ta aplikacja nie śledzi danych osobowych, ale szablon projektu zawiera funkcję zgody w przypadku, gdy jest to potrzebne do przestrzegania ogólnego rozporządzenia o [ochronie danych (RODO)](xref:security/gdpr)Unii Europejskiej.</span><span class="sxs-lookup"><span data-stu-id="81279-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub Indeks](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="81279-248">Na poniższej ilustracji przedstawiono aplikację po wyrażeniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="81279-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub Indeks](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="81279-250">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="81279-250">Examine the project files</span></span>

<span data-ttu-id="81279-251">Oto omówienie głównych folderów projektu i plików, z którymi będziesz pracować w późniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="81279-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="81279-252">Folder Strony</span><span class="sxs-lookup"><span data-stu-id="81279-252">Pages folder</span></span>

<span data-ttu-id="81279-253">Zawiera strony Razor i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="81279-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="81279-254">Każda strona Razor to para plików:</span><span class="sxs-lookup"><span data-stu-id="81279-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="81279-255">Plik *cshtml zawierający* znaczniki HTML z kodem C# przy użyciu składni Razor.</span><span class="sxs-lookup"><span data-stu-id="81279-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="81279-256">Plik *.cshtml.cs* zawierający kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="81279-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="81279-257">Pliki pomocnicze mają nazwy, które zaczynają się od podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="81279-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="81279-258">Na przykład plik *_Layout.cshtml* konfiguruje elementy interfejsu użytkownika wspólne dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="81279-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="81279-259">Ten plik konfiguruje menu nawigacyjne w górnej części strony i informacje o prawach autorskich u dołu strony.</span><span class="sxs-lookup"><span data-stu-id="81279-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="81279-260">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="81279-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="81279-261">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="81279-261">wwwroot folder</span></span>

<span data-ttu-id="81279-262">Zawiera pliki statyczne, takie jak pliki HTML, Pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="81279-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="81279-263">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="81279-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="81279-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="81279-264">appSettings.json</span></span>

<span data-ttu-id="81279-265">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="81279-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="81279-266">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="81279-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="81279-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="81279-267">Program.cs</span></span>

<span data-ttu-id="81279-268">Zawiera punkt wejścia programu.</span><span class="sxs-lookup"><span data-stu-id="81279-268">Contains the entry point for the program.</span></span> <span data-ttu-id="81279-269">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="81279-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="81279-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="81279-270">Startup.cs</span></span>

<span data-ttu-id="81279-271">Zawiera kod, który konfiguruje zachowanie aplikacji, na przykład czy wymaga zgody na pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="81279-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="81279-272">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="81279-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81279-273">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="81279-273">Additional resources</span></span>

* [<span data-ttu-id="81279-274">Wersja youtube tego samouczka</span><span class="sxs-lookup"><span data-stu-id="81279-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="81279-275">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="81279-275">Next steps</span></span>

<span data-ttu-id="81279-276">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="81279-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="81279-277">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="81279-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
