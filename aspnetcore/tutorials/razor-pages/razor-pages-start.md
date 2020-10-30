---
title: 'Samouczek: wprowadzenie do :::no-loc(Razor)::: stron w ASP.NET Core'
author: rick-anderson
description: 'W tej serii samouczków pokazano, jak używać :::no-loc(Razor)::: stron w ASP.NET Core. Dowiedz się, jak utworzyć model, wygenerować kod dla :::no-loc(Razor)::: stron, użyć Entity Framework Core i SQL Server na potrzeby dostępu do danych, dodać funkcję wyszukiwania, dodać sprawdzanie poprawności danych wejściowych i użyć migracji w celu zaktualizowania modelu.'
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060238"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="bfdc7-104">Samouczek: wprowadzenie do :::no-loc(Razor)::: stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfdc7-104">Tutorial: Get started with :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="bfdc7-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bfdc7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="bfdc7-106">Jest to pierwszy samouczek dotyczący serii, który uczy się podstaw tworzenia :::no-loc(Razor)::: aplikacji sieci web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="bfdc7-107">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="bfdc7-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bfdc7-109">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web dla stron.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-109">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="bfdc7-110">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-110">Run the app.</span></span>
> * <span data-ttu-id="bfdc7-111">Przejrzyj pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-111">Examine the project files.</span></span>

<span data-ttu-id="bfdc7-112">Na końcu tego samouczka będziesz mieć :::no-loc(Razor)::: aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-112">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="bfdc7-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="bfdc7-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdc7-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdc7-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdc7-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdc7-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdc7-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="bfdc7-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="bfdc7-118">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web dla stron</span><span class="sxs-lookup"><span data-stu-id="bfdc7-118">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdc7-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdc7-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bfdc7-120">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-120">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="bfdc7-121">Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-121">Create a new ASP.NET Core Web Application and select **Next** .</span></span>
  <span data-ttu-id="bfdc7-122">![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="bfdc7-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="bfdc7-123">Nazwij projekt **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-123">Name the project **:::no-loc(Razor):::PagesMovie** .</span></span> <span data-ttu-id="bfdc7-124">Ważne jest, aby nazwa projektu *:::no-loc(Razor)::: PagesMovie* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-124">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="bfdc7-125">![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="bfdc7-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="bfdc7-126">Wybierz pozycję **ASP.NET Core 3,1** na liście rozwijanej, **aplikacji sieci Web** , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create** .</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="bfdc7-128">Tworzony jest następujący projekt początkowy:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-128">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdc7-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdc7-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bfdc7-131">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bfdc7-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="bfdc7-132">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="bfdc7-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="bfdc7-134">`dotnet new`Polecenie tworzy nowy :::no-loc(Razor)::: Projekt strony w folderze *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-134">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="bfdc7-135">`code`Polecenie otwiera folder *:::no-loc(Razor)::: PagesMovie* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-135">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="bfdc7-136">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu " :::no-loc(Razor)::: PagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="bfdc7-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="bfdc7-137">Wybierz pozycję **Tak** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-137">Select **Yes** .</span></span>

  <span data-ttu-id="bfdc7-138">Katalog *. programu vscode* , zawierający *launch.json* i *tasks.jsdla* plików, jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdc7-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="bfdc7-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bfdc7-140">Wybierz pozycję **plik** > **nowe rozwiązanie** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-140">Select **File** > **New Solution** .</span></span>

  ![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="bfdc7-142">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **aplikacja internetowa aplikacji sieci Web** platformy .NET Core  >  **Next** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next** .</span></span> <span data-ttu-id="bfdc7-143">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli** aplikacji  >  **App**  >  **sieci Web**  >  **Next** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next** .</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="bfdc7-145">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="bfdc7-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="bfdc7-146">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-146">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="bfdc7-147">Jeśli jest prezentowana opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-147">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="bfdc7-148">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-148">Select **Next** .</span></span>

* <span data-ttu-id="bfdc7-149">Nazwij projekt **:::no-loc(Razor)::: PagesMovie** , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-149">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create** .</span></span>

  ![macOS Nazwij projekt](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="bfdc7-151">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bfdc7-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="bfdc7-152">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="bfdc7-152">Examine the project files</span></span>

<span data-ttu-id="bfdc7-153">Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="bfdc7-154">Folder stron</span><span class="sxs-lookup"><span data-stu-id="bfdc7-154">Pages folder</span></span>

<span data-ttu-id="bfdc7-155">Zawiera :::no-loc(Razor)::: strony i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-155">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="bfdc7-156">Każda :::no-loc(Razor)::: strona to para plików:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-156">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="bfdc7-157">Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# przy użyciu :::no-loc(Razor)::: składni.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-157">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="bfdc7-158">Plik *. cshtml.cs* , który zawiera kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="bfdc7-159">Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="bfdc7-160">Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="bfdc7-161">Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="bfdc7-162">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="bfdc7-163">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="bfdc7-163">wwwroot folder</span></span>

<span data-ttu-id="bfdc7-164">Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="bfdc7-165">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="bfdc7-166">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="bfdc7-166">appSettings.json</span></span>

<span data-ttu-id="bfdc7-167">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="bfdc7-168">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="bfdc7-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="bfdc7-169">Program.cs</span></span>

<span data-ttu-id="bfdc7-170">Zawiera punkt wejścia dla programu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-170">Contains the entry point for the program.</span></span> <span data-ttu-id="bfdc7-171">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="bfdc7-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="bfdc7-172">Startup.cs</span></span>

<span data-ttu-id="bfdc7-173">Zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="bfdc7-174">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bfdc7-175">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="bfdc7-175">Next steps</span></span>

<span data-ttu-id="bfdc7-176">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bfdc7-177">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="bfdc7-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bfdc7-178">Jest to pierwszy samouczek dotyczący serii.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="bfdc7-179">[Seria](xref:tutorials/razor-pages/index) uczy się podstaw tworzenia :::no-loc(Razor)::: aplikacji sieci Web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="bfdc7-180">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="bfdc7-181">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bfdc7-182">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web dla stron.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-182">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="bfdc7-183">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-183">Run the app.</span></span>
> * <span data-ttu-id="bfdc7-184">Przejrzyj pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-184">Examine the project files.</span></span>

<span data-ttu-id="bfdc7-185">Na końcu tego samouczka będziesz mieć :::no-loc(Razor)::: aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-185">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="bfdc7-187">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="bfdc7-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdc7-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdc7-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdc7-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdc7-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdc7-190">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="bfdc7-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="bfdc7-191">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web dla stron</span><span class="sxs-lookup"><span data-stu-id="bfdc7-191">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdc7-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdc7-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bfdc7-193">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-193">From the Visual Studio **File** menu, select **New** > **Project** .</span></span>

* <span data-ttu-id="bfdc7-194">Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-194">Create a new ASP.NET Core Web Application and select **Next** .</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="bfdc7-196">Nazwij projekt **:::no-loc(Razor)::: PagesMovie** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-196">Name the project **:::no-loc(Razor):::PagesMovie** .</span></span> <span data-ttu-id="bfdc7-197">Ważne jest, aby nazwa projektu *:::no-loc(Razor)::: PagesMovie* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-197">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="bfdc7-199">Wybierz pozycję **ASP.NET Core 2,2** na liście rozwijanej, **aplikacji sieci Web** , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create** .</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="bfdc7-201">Tworzony jest następujący projekt początkowy:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-201">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdc7-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdc7-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bfdc7-204">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bfdc7-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="bfdc7-205">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="bfdc7-206">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="bfdc7-207">`dotnet new`Polecenie tworzy nowy :::no-loc(Razor)::: Projekt strony w folderze *:::no-loc(Razor)::: PagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-207">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="bfdc7-208">`code`Polecenie otwiera folder *:::no-loc(Razor)::: PagesMovie* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-208">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="bfdc7-209">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu " :::no-loc(Razor)::: PagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="bfdc7-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="bfdc7-210">Wybierz pozycję **Tak** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-210">Select **Yes** .</span></span>

  <span data-ttu-id="bfdc7-211">Katalog *. programu vscode* , zawierający *launch.json* i *tasks.jsdla* plików, jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdc7-212">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="bfdc7-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bfdc7-213">Wybierz pozycję **plik** > **nowe rozwiązanie** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-213">Select **File** > **New Solution** .</span></span>

![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="bfdc7-215">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core** pozycję  >  **App**  >  **aplikacja internetowa aplikacji sieci Web** platformy .NET Core  >  **Next** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next** .</span></span> <span data-ttu-id="bfdc7-216">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli** aplikacji  >  **App**  >  **sieci Web**  >  **Next** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next** .</span></span>

* <span data-ttu-id="bfdc7-217">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="bfdc7-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="bfdc7-218">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-218">Confirm that **Authentication** is set to **No Authentication** .</span></span>
  * <span data-ttu-id="bfdc7-219">Jeśli jest prezentowana opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-219">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="bfdc7-220">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-220">Select **Next** .</span></span>

* <span data-ttu-id="bfdc7-221">Nazwij projekt **:::no-loc(Razor)::: PagesMovie** , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-221">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create** .</span></span>

  ![nameproj](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="bfdc7-223">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bfdc7-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bfdc7-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bfdc7-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bfdc7-225">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="bfdc7-226">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="bfdc7-227">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfdc7-228">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="bfdc7-229">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="bfdc7-230">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="bfdc7-231">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="bfdc7-232">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="bfdc7-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="bfdc7-234">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="bfdc7-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bfdc7-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="bfdc7-237">Naciśnij **klawisze CTRL + F5** , aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="bfdc7-238">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="bfdc7-239">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bfdc7-240">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="bfdc7-241">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="bfdc7-242">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="bfdc7-243">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="bfdc7-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="bfdc7-245">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bfdc7-247">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="bfdc7-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="bfdc7-248">Naciśnij **polecenie cmd-opt-F5** , aby uruchomić program bez debugera.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="bfdc7-249">Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i przechodzi do `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="bfdc7-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="bfdc7-250">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="bfdc7-251">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="bfdc7-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="bfdc7-253">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="bfdc7-255">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="bfdc7-255">Examine the project files</span></span>

<span data-ttu-id="bfdc7-256">Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="bfdc7-257">Folder stron</span><span class="sxs-lookup"><span data-stu-id="bfdc7-257">Pages folder</span></span>

<span data-ttu-id="bfdc7-258">Zawiera :::no-loc(Razor)::: strony i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-258">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="bfdc7-259">Każda :::no-loc(Razor)::: strona to para plików:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-259">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="bfdc7-260">Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# przy użyciu :::no-loc(Razor)::: składni.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-260">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="bfdc7-261">Plik *. cshtml.cs* , który zawiera kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="bfdc7-262">Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="bfdc7-263">Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="bfdc7-264">Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="bfdc7-265">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="bfdc7-266">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="bfdc7-266">wwwroot folder</span></span>

<span data-ttu-id="bfdc7-267">Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="bfdc7-268">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="bfdc7-269">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="bfdc7-269">appSettings.json</span></span>

<span data-ttu-id="bfdc7-270">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="bfdc7-271">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="bfdc7-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="bfdc7-272">Program.cs</span></span>

<span data-ttu-id="bfdc7-273">Zawiera punkt wejścia dla programu.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-273">Contains the entry point for the program.</span></span> <span data-ttu-id="bfdc7-274">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="bfdc7-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="bfdc7-275">Startup.cs</span></span>

<span data-ttu-id="bfdc7-276">Zawiera kod, który konfiguruje zachowanie aplikacji, na przykład czy wymaga zgody na polecenie :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-276">Contains code that configures app behavior, such as whether it requires consent for :::no-loc(cookie):::s.</span></span> <span data-ttu-id="bfdc7-277">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bfdc7-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bfdc7-278">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bfdc7-278">Additional resources</span></span>

* [<span data-ttu-id="bfdc7-279">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="bfdc7-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="bfdc7-280">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="bfdc7-280">Next steps</span></span>

<span data-ttu-id="bfdc7-281">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="bfdc7-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bfdc7-282">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="bfdc7-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
