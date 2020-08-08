---
title: 'Samouczek: wprowadzenie do Razor stron w ASP.NET Core'
author: rick-anderson
description: W tej serii samouczków pokazano, jak używać Razor stron w ASP.NET Core. Dowiedz się, jak utworzyć model, wygenerować kod dla Razor stron, użyć Entity Framework Core i SQL Server na potrzeby dostępu do danych, dodać funkcję wyszukiwania, dodać sprawdzanie poprawności danych wejściowych i użyć migracji w celu zaktualizowania modelu.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021097"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="a625f-104">Samouczek: wprowadzenie do Razor stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a625f-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a625f-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a625f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="a625f-106">Jest to pierwszy samouczek dotyczący serii, który uczy się podstaw tworzenia Razor aplikacji sieci web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="a625f-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a625f-107">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="a625f-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a625f-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a625f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a625f-109">Tworzenie Razor aplikacji sieci Web dla stron.</span><span class="sxs-lookup"><span data-stu-id="a625f-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a625f-110">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a625f-110">Run the app.</span></span>
> * <span data-ttu-id="a625f-111">Przejrzyj pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="a625f-111">Examine the project files.</span></span>

<span data-ttu-id="a625f-112">Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="a625f-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a625f-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a625f-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a625f-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a625f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a625f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a625f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a625f-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a625f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="a625f-118">Tworzenie Razor aplikacji sieci Web dla stron</span><span class="sxs-lookup"><span data-stu-id="a625f-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a625f-119">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a625f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a625f-120">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="a625f-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a625f-121">Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="a625f-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="a625f-122">![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="a625f-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="a625f-123">Nazwij projekt \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="a625f-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a625f-124">Ważne jest, aby nazwa projektu \* Razor PagesMovie\* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="a625f-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="a625f-125">![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="a625f-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="a625f-126">Wybierz pozycję **ASP.NET Core 3,1** na liście rozwijanej, **aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a625f-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="a625f-128">Tworzony jest następujący projekt początkowy:</span><span class="sxs-lookup"><span data-stu-id="a625f-128">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a625f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a625f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a625f-131">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a625f-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a625f-132">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="a625f-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a625f-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a625f-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a625f-134">`dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="a625f-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a625f-135">`code`Polecenie otwiera folder \* Razor PagesMovie\* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a625f-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a625f-136">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu " Razor PagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="a625f-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a625f-137">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="a625f-137">Select **Yes**.</span></span>

  <span data-ttu-id="a625f-138">Katalog *. programu vscode* , zawierający *launch.json* i *tasks.jsdla* plików, jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="a625f-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a625f-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a625f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a625f-140">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="a625f-140">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a625f-142">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **aplikacja internetowa aplikacji sieci Web**platformy .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="a625f-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a625f-143">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**aplikacji  >  **App**  >  **sieci Web**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="a625f-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Wybieranie szablonu aplikacji sieci Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="a625f-145">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="a625f-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a625f-146">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="a625f-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a625f-147">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 3. x.</span><span class="sxs-lookup"><span data-stu-id="a625f-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="a625f-148">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="a625f-148">Select **Next**.</span></span>

* <span data-ttu-id="a625f-149">Nazwij projekt \*\* Razor PagesMovie\*\*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a625f-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS Nazwij projekt](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a625f-151">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="a625f-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="a625f-152">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="a625f-152">Examine the project files</span></span>

<span data-ttu-id="a625f-153">Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="a625f-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a625f-154">Folder stron</span><span class="sxs-lookup"><span data-stu-id="a625f-154">Pages folder</span></span>

<span data-ttu-id="a625f-155">Zawiera Razor strony i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="a625f-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a625f-156">Każda Razor strona to para plików:</span><span class="sxs-lookup"><span data-stu-id="a625f-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a625f-157">Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# przy użyciu Razor składni.</span><span class="sxs-lookup"><span data-stu-id="a625f-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a625f-158">Plik *. cshtml.cs* , który zawiera kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="a625f-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a625f-159">Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="a625f-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a625f-160">Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="a625f-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a625f-161">Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony.</span><span class="sxs-lookup"><span data-stu-id="a625f-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a625f-162">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a625f-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a625f-163">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="a625f-163">wwwroot folder</span></span>

<span data-ttu-id="a625f-164">Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="a625f-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a625f-165">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a625f-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a625f-166">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="a625f-166">appSettings.json</span></span>

<span data-ttu-id="a625f-167">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="a625f-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a625f-168">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a625f-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a625f-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="a625f-169">Program.cs</span></span>

<span data-ttu-id="a625f-170">Zawiera punkt wejścia dla programu.</span><span class="sxs-lookup"><span data-stu-id="a625f-170">Contains the entry point for the program.</span></span> <span data-ttu-id="a625f-171">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a625f-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a625f-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a625f-172">Startup.cs</span></span>

<span data-ttu-id="a625f-173">Zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a625f-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="a625f-174">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a625f-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a625f-175">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="a625f-175">Next steps</span></span>

<span data-ttu-id="a625f-176">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="a625f-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a625f-177">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="a625f-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a625f-178">Jest to pierwszy samouczek dotyczący serii.</span><span class="sxs-lookup"><span data-stu-id="a625f-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="a625f-179">[Seria](xref:tutorials/razor-pages/index) uczy się podstaw tworzenia Razor aplikacji sieci Web ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="a625f-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a625f-180">Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.</span><span class="sxs-lookup"><span data-stu-id="a625f-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a625f-181">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a625f-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a625f-182">Tworzenie Razor aplikacji sieci Web dla stron.</span><span class="sxs-lookup"><span data-stu-id="a625f-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a625f-183">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a625f-183">Run the app.</span></span>
> * <span data-ttu-id="a625f-184">Przejrzyj pliki projektu.</span><span class="sxs-lookup"><span data-stu-id="a625f-184">Examine the project files.</span></span>

<span data-ttu-id="a625f-185">Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="a625f-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a625f-187">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a625f-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a625f-188">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a625f-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a625f-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a625f-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a625f-190">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a625f-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="a625f-191">Tworzenie Razor aplikacji sieci Web dla stron</span><span class="sxs-lookup"><span data-stu-id="a625f-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a625f-192">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a625f-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a625f-193">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="a625f-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="a625f-194">Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="a625f-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="a625f-196">Nazwij projekt \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="a625f-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a625f-197">Ważne jest, aby nazwa projektu \* Razor PagesMovie\* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="a625f-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="a625f-199">Wybierz pozycję **ASP.NET Core 2,2** na liście rozwijanej, **aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a625f-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="a625f-201">Tworzony jest następujący projekt początkowy:</span><span class="sxs-lookup"><span data-stu-id="a625f-201">The following starter project is created:</span></span>

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a625f-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a625f-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a625f-204">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a625f-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a625f-205">Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="a625f-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a625f-206">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a625f-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a625f-207">`dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="a625f-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a625f-208">`code`Polecenie otwiera folder \* Razor PagesMovie\* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a625f-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a625f-209">Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu " Razor PagesMovie". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="a625f-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a625f-210">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="a625f-210">Select **Yes**.</span></span>

  <span data-ttu-id="a625f-211">Katalog *. programu vscode* , zawierający *launch.json* i *tasks.jsdla* plików, jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="a625f-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a625f-212">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a625f-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a625f-213">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="a625f-213">Select **File** > **New Solution**.</span></span>

![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a625f-215">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz **.NET Core**pozycję  >  **App**  >  **aplikacja internetowa aplikacji sieci Web**platformy .NET Core  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="a625f-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a625f-216">W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**aplikacji  >  **App**  >  **sieci Web**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="a625f-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="a625f-217">W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="a625f-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a625f-218">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="a625f-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a625f-219">Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 2. x.</span><span class="sxs-lookup"><span data-stu-id="a625f-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="a625f-220">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="a625f-220">Select **Next**.</span></span>

* <span data-ttu-id="a625f-221">Nazwij projekt \*\* Razor PagesMovie\*\*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a625f-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a625f-223">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="a625f-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a625f-224">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a625f-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a625f-225">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="a625f-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a625f-226">Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację.</span><span class="sxs-lookup"><span data-stu-id="a625f-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a625f-227">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="a625f-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a625f-228">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="a625f-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a625f-229">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="a625f-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a625f-230">Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.</span><span class="sxs-lookup"><span data-stu-id="a625f-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="a625f-231">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="a625f-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a625f-232">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a625f-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a625f-234">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="a625f-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="a625f-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a625f-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a625f-237">Naciśnij **klawisze CTRL + F5** , aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="a625f-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a625f-238">Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a625f-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a625f-239">Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` .</span><span class="sxs-lookup"><span data-stu-id="a625f-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a625f-240">Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="a625f-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a625f-241">Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.</span><span class="sxs-lookup"><span data-stu-id="a625f-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="a625f-242">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="a625f-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a625f-243">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a625f-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a625f-245">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="a625f-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a625f-247">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a625f-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a625f-248">Naciśnij **polecenie cmd-opt-F5** , aby uruchomić program bez debugera.</span><span class="sxs-lookup"><span data-stu-id="a625f-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a625f-249">Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i przechodzi do `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a625f-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="a625f-250">Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.</span><span class="sxs-lookup"><span data-stu-id="a625f-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a625f-251">Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a625f-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="a625f-253">Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:</span><span class="sxs-lookup"><span data-stu-id="a625f-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Strona główna lub indeks](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="a625f-255">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="a625f-255">Examine the project files</span></span>

<span data-ttu-id="a625f-256">Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="a625f-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a625f-257">Folder stron</span><span class="sxs-lookup"><span data-stu-id="a625f-257">Pages folder</span></span>

<span data-ttu-id="a625f-258">Zawiera Razor strony i pliki pomocnicze.</span><span class="sxs-lookup"><span data-stu-id="a625f-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a625f-259">Każda Razor strona to para plików:</span><span class="sxs-lookup"><span data-stu-id="a625f-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a625f-260">Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# przy użyciu Razor składni.</span><span class="sxs-lookup"><span data-stu-id="a625f-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a625f-261">Plik *. cshtml.cs* , który zawiera kod C#, który obsługuje zdarzenia strony.</span><span class="sxs-lookup"><span data-stu-id="a625f-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a625f-262">Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="a625f-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a625f-263">Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron.</span><span class="sxs-lookup"><span data-stu-id="a625f-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a625f-264">Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony.</span><span class="sxs-lookup"><span data-stu-id="a625f-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a625f-265">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a625f-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a625f-266">folder wwwroot</span><span class="sxs-lookup"><span data-stu-id="a625f-266">wwwroot folder</span></span>

<span data-ttu-id="a625f-267">Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS.</span><span class="sxs-lookup"><span data-stu-id="a625f-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a625f-268">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a625f-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a625f-269">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="a625f-269">appSettings.json</span></span>

<span data-ttu-id="a625f-270">Zawiera dane konfiguracyjne, takie jak parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="a625f-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a625f-271">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a625f-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a625f-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="a625f-272">Program.cs</span></span>

<span data-ttu-id="a625f-273">Zawiera punkt wejścia dla programu.</span><span class="sxs-lookup"><span data-stu-id="a625f-273">Contains the entry point for the program.</span></span> <span data-ttu-id="a625f-274">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a625f-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a625f-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a625f-275">Startup.cs</span></span>

<span data-ttu-id="a625f-276">Zawiera kod, który konfiguruje zachowanie aplikacji, na przykład czy wymaga zgody na polecenie cookie s.</span><span class="sxs-lookup"><span data-stu-id="a625f-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="a625f-277">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a625f-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a625f-278">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a625f-278">Additional resources</span></span>

* [<span data-ttu-id="a625f-279">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="a625f-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="a625f-280">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="a625f-280">Next steps</span></span>

<span data-ttu-id="a625f-281">Przejdź do następnego samouczka z serii:</span><span class="sxs-lookup"><span data-stu-id="a625f-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a625f-282">Dodawanie modelu</span><span class="sxs-lookup"><span data-stu-id="a625f-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
