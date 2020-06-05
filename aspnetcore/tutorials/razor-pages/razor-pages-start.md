---
title: "\"Samouczek: wprowadzenie do Razor stron w ASP.NET Core\""
author: rick-anderson
description: W tej serii samouczków pokazano, jak używać Razor stron w ASP.NET Core. Dowiedz się, jak utworzyć model, wygenerować kod dla Razor stron, użyć Entity Framework Core i SQL Server na potrzeby dostępu do danych, dodać funkcję wyszukiwania, dodać sprawdzanie poprawności danych wejściowych i użyć migracji w celu zaktualizowania modelu.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452353"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="dbf54-104">Samouczek: wprowadzenie do Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbf54-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="dbf54-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dbf54-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="dbf54-106">Jest to pierwszy samouczek dotyczący serii, który uczy się podstaw tworzenia Razor aplikacji sieci web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="dbf54-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="dbf54-107">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="dbf54-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="dbf54-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dbf54-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dbf54-109">Tworzenie Razor aplikacji sieci Web dla stron.</span><span class="sxs-lookup"><span data-stu-id="dbf54-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="dbf54-110">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="dbf54-110">Run the app.</span></span>
> * <span data-ttu-id="dbf54-111">Przejrzyj pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-111">Examine the project files.</span></span>

<span data-ttu-id="dbf54-112">Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="dbf54-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="dbf54-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="dbf54-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbf54-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbf54-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbf54-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbf54-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbf54-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="dbf54-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="dbf54-118">Tworzenie Razor aplikacji sieci Web dla stron</span><span class="sxs-lookup"><span data-stu-id="dbf54-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbf54-119">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbf54-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dbf54-120">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dbf54-121">Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="dbf54-122">![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="dbf54-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="dbf54-123">Nazwij projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="dbf54-124">Ważne jest, aby nazwa projektu *RazorPagesMovie* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="dbf54-125">![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="dbf54-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="dbf54-126">Wybierz pozycję **ASP.NET Core 3,1** na liście rozwijanej, **aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="dbf54-128">Tworzony jest następujący projekt początkowy:</span><span class="sxs-lookup"><span data-stu-id="dbf54-128">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbf54-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbf54-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dbf54-131">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dbf54-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="dbf54-132">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="dbf54-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="dbf54-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="dbf54-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="dbf54-134">`dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="dbf54-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="dbf54-135">`code`Polecenie otwiera folder *RazorPagesMovie* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dbf54-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="dbf54-136">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "RazorPagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="dbf54-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="dbf54-137">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-137">Select **Yes**.</span></span>

  <span data-ttu-id="dbf54-138">Katalog *. programu vscode* , zawierający pliki *Launch. JSON* i *Tasks. JSON* , jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbf54-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="dbf54-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dbf54-140">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-140">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="dbf54-142">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **aplikacja internetowa aplikacji sieci Web**platformy .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="dbf54-143">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**aplikacji  >  **App**  >  **sieci Web**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="dbf54-145">Potwierdź następujące konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="dbf54-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="dbf54-146">**Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="dbf54-147">**Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="dbf54-148">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-148">Select **Next**.</span></span>

  ![wybór macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="dbf54-150">Nazwij projekt **RazorPagesMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="dbf54-152">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="dbf54-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="dbf54-153">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="dbf54-153">Examine the project files</span></span>

<span data-ttu-id="dbf54-154">Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="dbf54-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="dbf54-155">Folder stron</span><span class="sxs-lookup"><span data-stu-id="dbf54-155">Pages folder</span></span>

<span data-ttu-id="dbf54-156">Zawiera Razor strony i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="dbf54-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="dbf54-157">Każda Razor strona to para plików:</span><span class="sxs-lookup"><span data-stu-id="dbf54-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="dbf54-158">Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# przy użyciu Razor składni.</span><span class="sxs-lookup"><span data-stu-id="dbf54-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="dbf54-159">Plik *. cshtml.cs* , który zawiera kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="dbf54-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="dbf54-160">Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="dbf54-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="dbf54-161">Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="dbf54-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="dbf54-162">Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony.</span><span class="sxs-lookup"><span data-stu-id="dbf54-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="dbf54-163">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="dbf54-164">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="dbf54-164">wwwroot folder</span></span>

<span data-ttu-id="dbf54-165">Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="dbf54-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="dbf54-166">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="dbf54-167">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="dbf54-167">appSettings.json</span></span>

<span data-ttu-id="dbf54-168">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="dbf54-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="dbf54-169">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="dbf54-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="dbf54-170">Program.cs</span></span>

<span data-ttu-id="dbf54-171">Zawiera punkt wejścia dla programu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-171">Contains the entry point for the program.</span></span> <span data-ttu-id="dbf54-172">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="dbf54-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="dbf54-173">Startup.cs</span></span>

<span data-ttu-id="dbf54-174">Zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dbf54-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="dbf54-175">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dbf54-176">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="dbf54-176">Next steps</span></span>

<span data-ttu-id="dbf54-177">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="dbf54-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="dbf54-178">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="dbf54-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dbf54-179">Jest to pierwszy samouczek dotyczący serii.</span><span class="sxs-lookup"><span data-stu-id="dbf54-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="dbf54-180">[Seria](xref:tutorials/razor-pages/index) uczy się podstaw tworzenia Razor aplikacji sieci Web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="dbf54-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="dbf54-181">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="dbf54-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="dbf54-182">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dbf54-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dbf54-183">Tworzenie Razor aplikacji sieci Web dla stron.</span><span class="sxs-lookup"><span data-stu-id="dbf54-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="dbf54-184">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="dbf54-184">Run the app.</span></span>
> * <span data-ttu-id="dbf54-185">Przejrzyj pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-185">Examine the project files.</span></span>

<span data-ttu-id="dbf54-186">Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="dbf54-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="dbf54-188">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="dbf54-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbf54-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbf54-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbf54-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbf54-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbf54-191">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="dbf54-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="dbf54-192">Tworzenie Razor aplikacji sieci Web dla stron</span><span class="sxs-lookup"><span data-stu-id="dbf54-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbf54-193">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbf54-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dbf54-194">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="dbf54-195">Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="dbf54-197">Nazwij projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="dbf54-198">Ważne jest, aby nazwa projektu *RazorPagesMovie* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="dbf54-200">Wybierz pozycję **ASP.NET Core 2,2** na liście rozwijanej, **aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="dbf54-202">Tworzony jest następujący projekt początkowy:</span><span class="sxs-lookup"><span data-stu-id="dbf54-202">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbf54-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbf54-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dbf54-205">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dbf54-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="dbf54-206">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="dbf54-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="dbf54-207">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="dbf54-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="dbf54-208">`dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="dbf54-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="dbf54-209">`code`Polecenie otwiera folder *RazorPagesMovie* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dbf54-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="dbf54-210">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu "RazorPagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="dbf54-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="dbf54-211">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-211">Select **Yes**.</span></span>

  <span data-ttu-id="dbf54-212">Katalog *. programu vscode* , zawierający pliki *Launch. JSON* i *Tasks. JSON* , jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbf54-213">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="dbf54-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dbf54-214">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-214">Select **File** > **New Solution**.</span></span>

![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="dbf54-216">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **aplikacja internetowa aplikacji sieci Web**platformy .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="dbf54-217">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**aplikacji  >  **App**  >  **sieci Web**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="dbf54-218">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** Ustaw platformę **docelową** na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![wybór macOS .NET Core 3,0](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="dbf54-220">Nazwij projekt **RazorPagesMovie**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="dbf54-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="dbf54-222">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="dbf54-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbf54-223">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbf54-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dbf54-224">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="dbf54-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="dbf54-225">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="dbf54-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="dbf54-226">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="dbf54-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dbf54-227">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="dbf54-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="dbf54-228">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="dbf54-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="dbf54-229">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="dbf54-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="dbf54-230">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="dbf54-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="dbf54-231">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dbf54-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="dbf54-233">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="dbf54-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="dbf54-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbf54-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="dbf54-236">Naciśnij **klawisze CTRL + F5** , aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="dbf54-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="dbf54-237">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="dbf54-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="dbf54-238">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="dbf54-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="dbf54-239">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="dbf54-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="dbf54-240">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="dbf54-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="dbf54-241">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="dbf54-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="dbf54-242">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dbf54-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="dbf54-244">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="dbf54-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbf54-246">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="dbf54-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="dbf54-247">Naciśnij **polecenie cmd-opt-F5** , aby uruchomić program bez debugera.</span><span class="sxs-lookup"><span data-stu-id="dbf54-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="dbf54-248">Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i przechodzi do `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="dbf54-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="dbf54-249">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="dbf54-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="dbf54-250">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="dbf54-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="dbf54-252">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="dbf54-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="dbf54-254">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="dbf54-254">Examine the project files</span></span>

<span data-ttu-id="dbf54-255">Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="dbf54-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="dbf54-256">Folder stron</span><span class="sxs-lookup"><span data-stu-id="dbf54-256">Pages folder</span></span>

<span data-ttu-id="dbf54-257">Zawiera Razor strony i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="dbf54-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="dbf54-258">Każda Razor strona to para plików:</span><span class="sxs-lookup"><span data-stu-id="dbf54-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="dbf54-259">Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# przy użyciu Razor składni.</span><span class="sxs-lookup"><span data-stu-id="dbf54-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="dbf54-260">Plik *. cshtml.cs* , który zawiera kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="dbf54-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="dbf54-261">Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="dbf54-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="dbf54-262">Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="dbf54-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="dbf54-263">Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony.</span><span class="sxs-lookup"><span data-stu-id="dbf54-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="dbf54-264">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="dbf54-265">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="dbf54-265">wwwroot folder</span></span>

<span data-ttu-id="dbf54-266">Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="dbf54-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="dbf54-267">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="dbf54-268">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="dbf54-268">appSettings.json</span></span>

<span data-ttu-id="dbf54-269">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="dbf54-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="dbf54-270">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="dbf54-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="dbf54-271">Program.cs</span></span>

<span data-ttu-id="dbf54-272">Zawiera punkt wejścia dla programu.</span><span class="sxs-lookup"><span data-stu-id="dbf54-272">Contains the entry point for the program.</span></span> <span data-ttu-id="dbf54-273">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="dbf54-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="dbf54-274">Startup.cs</span></span>

<span data-ttu-id="dbf54-275">Zawiera kod, który konfiguruje zachowanie aplikacji, na przykład czy wymaga zgody na pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="dbf54-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="dbf54-276">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="dbf54-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbf54-277">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="dbf54-277">Additional resources</span></span>

* [<span data-ttu-id="dbf54-278">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="dbf54-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="dbf54-279">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="dbf54-279">Next steps</span></span>

<span data-ttu-id="dbf54-280">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="dbf54-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="dbf54-281">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="dbf54-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
