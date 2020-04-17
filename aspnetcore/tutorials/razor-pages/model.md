---
title: Dodawanie modelu do aplikacji Razor Pages w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać klasy do zarządzania filmami w bazie danych przy użyciu entity framework core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488874"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="2162d-103">Dodawanie modelu do aplikacji Razor Pages w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2162d-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2162d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2162d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2162d-105">W tej sekcji klasy są dodawane do zarządzania filmami.</span><span class="sxs-lookup"><span data-stu-id="2162d-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="2162d-106">Klasy modelu aplikacji używają [entity framework core (EF Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2162d-107">EF Core jest obiektokręgowym maperem (O/RM), który upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="2162d-108">Klasy modelu są znane jako klasy POCO (z "zwykłych starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="2162d-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2162d-109">Definiują właściwości danych, które są przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2162d-110">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="2162d-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-112">Kliknij prawym przyciskiem myszy projekt **RazorPagesMovie** > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2162d-113">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="2162d-113">Name the folder *Models*.</span></span>

<span data-ttu-id="2162d-114">Kliknij prawym przyciskiem myszy folder *Modele.*</span><span class="sxs-lookup"><span data-stu-id="2162d-114">Right click the *Models* folder.</span></span> <span data-ttu-id="2162d-115">Wybierz **pozycję Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="2162d-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="2162d-116">Nazwij klasę **Movie**.</span><span class="sxs-lookup"><span data-stu-id="2162d-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2162d-118">Dodawanie folderu o nazwie *Modele*.</span><span class="sxs-lookup"><span data-stu-id="2162d-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2162d-119">Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2162d-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-120">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2162d-121">W witrynie Solution Pad kliknij prawym przyciskiem myszy projekt **RazorPagesMovie,** a następnie wybierz polecenie **Dodaj** > **nowy folder...**. Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="2162d-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="2162d-122">Kliknij prawym przyciskiem myszy folder *Modele,* a następnie wybierz polecenie **Dodaj** > **nowy plik...**.</span><span class="sxs-lookup"><span data-stu-id="2162d-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="2162d-123">W oknie dialogowym **Nowy plik:**</span><span class="sxs-lookup"><span data-stu-id="2162d-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2162d-124">Wybierz **pozycję Ogólne** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="2162d-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2162d-125">Wybierz **pozycję Pusta klasa** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="2162d-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2162d-126">Nazwij klasę **Film** i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="2162d-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2162d-127">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilacji.</span><span class="sxs-lookup"><span data-stu-id="2162d-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2162d-128">Rusztowanie modelu filmu</span><span class="sxs-lookup"><span data-stu-id="2162d-128">Scaffold the movie model</span></span>

<span data-ttu-id="2162d-129">W tej sekcji model filmu jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="2162d-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2162d-130">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="2162d-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-132">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="2162d-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2162d-133">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2162d-134">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="2162d-134">Name the folder *Movies*</span></span>

<span data-ttu-id="2162d-135">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="2162d-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="2162d-137">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="2162d-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="2162d-139">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="2162d-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2162d-140">W rozwijaniu **klasy Model** wybierz pozycję **Film (RazorPagesMovie.Models).**</span><span class="sxs-lookup"><span data-stu-id="2162d-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2162d-141">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zmień wygenerowaną nazwę z RazorPagesMovie. **Modele**. RazorPagesMovieContext do RazorPagesMovie. **Dane**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2162d-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2162d-142">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="2162d-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2162d-143">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="2162d-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2162d-144">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2162d-144">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="2162d-146">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2162d-148">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="2162d-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2162d-149">Zainstaluj narzędzie rusztowania:</span><span class="sxs-lookup"><span data-stu-id="2162d-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="2162d-150">**W systemie Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2162d-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2162d-151">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2162d-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-152">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2162d-153">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="2162d-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2162d-154">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2162d-155">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="2162d-155">Name the folder *Movies*</span></span>

<span data-ttu-id="2162d-156">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowe rusztowania...**.</span><span class="sxs-lookup"><span data-stu-id="2162d-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="2162d-158">W oknie dialogowym **Nowe rusztowania** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="2162d-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2162d-160">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="2162d-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2162d-161">W rozwijaniu **klasy Modelu** wybierz lub wpisz **film (RazorPagesMovie.Models).**</span><span class="sxs-lookup"><span data-stu-id="2162d-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2162d-162">W wierszu **klasy Kontekst danych** wpisz nazwę nowej klasy RazorPagesMovie. **Dane**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2162d-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2162d-163">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="2162d-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2162d-164">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="2162d-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2162d-165">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2162d-165">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="2162d-167">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="2162d-168">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="2162d-168">Add EF tools</span></span>

<span data-ttu-id="2162d-169">Uruchom następujące polecenie .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="2162d-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="2162d-170">Poprzednie polecenie dodaje podstawowe narzędzia entity framework dla interfejsu wiersza polecenia .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2162d-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="2162d-171">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="2162d-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-173">Proces rusztowania tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2162d-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2162d-174">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="2162d-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2162d-175">*Dane/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2162d-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2162d-176">Zaktualizowano</span><span class="sxs-lookup"><span data-stu-id="2162d-176">Updated</span></span>

* <span data-ttu-id="2162d-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2162d-177">*Startup.cs*</span></span>

<span data-ttu-id="2162d-178">Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2162d-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-179">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2162d-180">Proces rusztowania tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2162d-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2162d-181">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="2162d-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2162d-182">*Dane/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2162d-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2162d-183">Zaktualizowano</span><span class="sxs-lookup"><span data-stu-id="2162d-183">Updated</span></span>

* <span data-ttu-id="2162d-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2162d-184">*Startup.cs*</span></span>

<span data-ttu-id="2162d-185">Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2162d-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2162d-187">Proces rusztowania tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2162d-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2162d-188">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="2162d-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2162d-189">Utworzone pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2162d-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2162d-190">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="2162d-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-192">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="2162d-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2162d-193">Dodaj migrację początkową.</span><span class="sxs-lookup"><span data-stu-id="2162d-193">Add an initial migration.</span></span>
* <span data-ttu-id="2162d-194">Zaktualizuj bazę danych za pomocą migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="2162d-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="2162d-195">Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package** Manager .</span><span class="sxs-lookup"><span data-stu-id="2162d-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2162d-197">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2162d-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-199">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="2162d-200">Poprzednie polecenia generują następujące ostrzeżenie: "Dla kolumny dziesiętnej "Cena" dla kolumny dziesiętnej nie określono żadnego typu "Cena".</span><span class="sxs-lookup"><span data-stu-id="2162d-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2162d-201">Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="2162d-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2162d-202">Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".</span><span class="sxs-lookup"><span data-stu-id="2162d-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2162d-203">Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="2162d-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="2162d-204">Polecenie migracji generuje kod do tworzenia początkowego schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="2162d-205">Schemat jest oparty na modelu `DbContext`określonym w programie .</span><span class="sxs-lookup"><span data-stu-id="2162d-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2162d-206">Argument `InitialCreate` jest używany do nazywania migracji.</span><span class="sxs-lookup"><span data-stu-id="2162d-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2162d-207">Można użyć dowolnej nazwy, ale zgodnie z konwencją wybrano nazwę opisującą migrację.</span><span class="sxs-lookup"><span data-stu-id="2162d-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2162d-208">Polecenie `update` uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="2162d-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2162d-209">W takim `update` przypadku `Up` uruchamia metodę w *migrations/\<time-stamp>_InitialCreate.cs* file, który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2162d-211">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="2162d-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2162d-212">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2162d-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2162d-213">Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2162d-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2162d-214">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2162d-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2162d-215">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="2162d-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2162d-216">Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2162d-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2162d-217">Sprawdź `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="2162d-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2162d-218">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="2162d-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2162d-219">Współrzędne `RazorPagesMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu.</span><span class="sxs-lookup"><span data-stu-id="2162d-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2162d-220">Kontekst danych`RazorPagesMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2162d-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2162d-221">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2162d-222">Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2162d-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2162d-223">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2162d-224">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2162d-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2162d-225">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="2162d-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2162d-226">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2162d-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2162d-228">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="2162d-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-229">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2162d-230">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="2162d-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2162d-231">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2162d-231">Test the app</span></span>

* <span data-ttu-id="2162d-232">Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).</span><span class="sxs-lookup"><span data-stu-id="2162d-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2162d-233">Jeśli zostanie wyświetlony błąd:</span><span class="sxs-lookup"><span data-stu-id="2162d-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2162d-234">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2162d-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2162d-235">Przetestuj łącze **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="2162d-235">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2162d-237">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="2162d-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2162d-238">Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana.</span><span class="sxs-lookup"><span data-stu-id="2162d-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2162d-239">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="2162d-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2162d-240">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="2162d-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2162d-241">Następny samouczek wyjaśnia pliki utworzone przez szkielety.</span><span class="sxs-lookup"><span data-stu-id="2162d-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2162d-242">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2162d-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2162d-243">[Poprzedni: Zacznij](xref:tutorials/razor-pages/razor-pages-start)
> [dalej: Scaffolded Strony brzytwy](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2162d-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2162d-244">W tej sekcji klasy są dodawane do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2162d-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2162d-245">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych SQLite.</span><span class="sxs-lookup"><span data-stu-id="2162d-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2162d-246">Klasy modelu aplikacji są używane z [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2162d-247">EF Core jest strukturą mapowania obiekto-relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2162d-248">Klasy modelu są znane jako klasy POCO (z "zwykłych starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="2162d-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2162d-249">Definiują właściwości danych, które są przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2162d-250">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="2162d-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-252">Kliknij prawym przyciskiem myszy projekt **RazorPagesMovie** > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2162d-253">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="2162d-253">Name the folder *Models*.</span></span>

<span data-ttu-id="2162d-254">Kliknij prawym przyciskiem myszy folder *Modele.*</span><span class="sxs-lookup"><span data-stu-id="2162d-254">Right click the *Models* folder.</span></span> <span data-ttu-id="2162d-255">Wybierz **pozycję Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="2162d-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="2162d-256">Nazwij klasę **Movie**.</span><span class="sxs-lookup"><span data-stu-id="2162d-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2162d-258">Dodawanie folderu o nazwie *Modele*.</span><span class="sxs-lookup"><span data-stu-id="2162d-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2162d-259">Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2162d-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-260">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2162d-261">W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **RazorPagesMovie,** a następnie wybierz polecenie **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2162d-262">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="2162d-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="2162d-263">Kliknij prawym przyciskiem myszy folder *Modele,* a następnie wybierz polecenie **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="2162d-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2162d-264">W oknie dialogowym **Nowy plik:**</span><span class="sxs-lookup"><span data-stu-id="2162d-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2162d-265">Wybierz **pozycję Ogólne** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="2162d-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2162d-266">Wybierz **pozycję Pusta klasa** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="2162d-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2162d-267">Nazwij klasę **Film** i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="2162d-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2162d-268">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilacji.</span><span class="sxs-lookup"><span data-stu-id="2162d-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2162d-269">Rusztowanie modelu filmu</span><span class="sxs-lookup"><span data-stu-id="2162d-269">Scaffold the movie model</span></span>

<span data-ttu-id="2162d-270">W tej sekcji model filmu jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="2162d-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2162d-271">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="2162d-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-273">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="2162d-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2162d-274">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2162d-275">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="2162d-275">Name the folder *Movies*</span></span>

<span data-ttu-id="2162d-276">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="2162d-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="2162d-278">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="2162d-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="2162d-280">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="2162d-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="2162d-281">W rozwijaniu **klasy Model** wybierz pozycję **Film (RazorPagesMovie.Models).**</span><span class="sxs-lookup"><span data-stu-id="2162d-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2162d-282">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zaakceptuj wygenerowaną nazwę **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="2162d-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2162d-283">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2162d-283">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="2162d-285">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2162d-287">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="2162d-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="2162d-288">**W systemie Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2162d-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2162d-289">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2162d-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-290">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2162d-291">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="2162d-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2162d-292">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2162d-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2162d-293">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="2162d-293">Name the folder *Movies*</span></span>

<span data-ttu-id="2162d-294">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="2162d-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="2162d-296">W oknie dialogowym **Dodawanie nowych rusztowań** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="2162d-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2162d-298">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="2162d-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2162d-299">W rozwijaniu **klasy Model** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="2162d-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="2162d-300">W wierszu **klasy Kontekst danych** wpisz opcję **RazorPagesMovieContext** spowoduje utworzenie nowej klasy kontekstu db z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="2162d-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="2162d-301">W tym przypadku będzie to **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="2162d-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2162d-302">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2162d-302">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="2162d-304">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="2162d-305">Proces rusztowania tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2162d-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2162d-306">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="2162d-306">Files created</span></span>

* <span data-ttu-id="2162d-307">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="2162d-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2162d-308">*Dane/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2162d-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2162d-309">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="2162d-309">File updated</span></span>

* <span data-ttu-id="2162d-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2162d-310">*Startup.cs*</span></span>

<span data-ttu-id="2162d-311">Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2162d-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2162d-312">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="2162d-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2162d-314">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="2162d-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2162d-315">Dodaj migrację początkową.</span><span class="sxs-lookup"><span data-stu-id="2162d-315">Add an initial migration.</span></span>
* <span data-ttu-id="2162d-316">Zaktualizuj bazę danych za pomocą migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="2162d-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="2162d-317">Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package** Manager .</span><span class="sxs-lookup"><span data-stu-id="2162d-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2162d-319">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2162d-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2162d-320">Polecenie `Add-Migration` generuje kod do tworzenia początkowego schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2162d-321">Schemat jest oparty na modelu określonym `DbContext` w pliku (W *RazorPagesMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="2162d-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2162d-322">Argument `InitialCreate` jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="2162d-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="2162d-323">Można użyć dowolnej nazwy, ale zgodnie z konwencją używana jest nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="2162d-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2162d-324">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="2162d-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="2162d-325">Polecenie `Update-Database` uruchamia `Up` metodę w pliku *Migrations/\<time-stamp>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="2162d-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2162d-326">Metoda `Up` tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-328">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="2162d-329">Poprzednie polecenia generują następujące ostrzeżenie: "*Dla kolumny dziesiętnej "Cena" dla kolumny dziesiętnej "Cena" dla typu encji "Film". Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".*" Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="2162d-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2162d-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2162d-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2162d-331">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="2162d-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2162d-332">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2162d-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2162d-333">Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2162d-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2162d-334">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2162d-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2162d-335">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="2162d-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2162d-336">Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2162d-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2162d-337">Sprawdź `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="2162d-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2162d-338">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="2162d-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2162d-339">Współrzędne `RazorPagesMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu.</span><span class="sxs-lookup"><span data-stu-id="2162d-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2162d-340">Kontekst danych`RazorPagesMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2162d-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2162d-341">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2162d-342">Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2162d-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2162d-343">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2162d-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2162d-344">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2162d-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2162d-345">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="2162d-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2162d-346">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2162d-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2162d-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2162d-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2162d-348">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="2162d-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2162d-349">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2162d-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2162d-350">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="2162d-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2162d-351">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2162d-351">Test the app</span></span>

* <span data-ttu-id="2162d-352">Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).</span><span class="sxs-lookup"><span data-stu-id="2162d-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2162d-353">Jeśli zostanie wyświetlony błąd:</span><span class="sxs-lookup"><span data-stu-id="2162d-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2162d-354">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2162d-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2162d-355">Przetestuj łącze **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="2162d-355">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2162d-357">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="2162d-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2162d-358">Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana.</span><span class="sxs-lookup"><span data-stu-id="2162d-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2162d-359">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="2162d-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2162d-360">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="2162d-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2162d-361">Następny samouczek wyjaśnia pliki utworzone przez szkielety.</span><span class="sxs-lookup"><span data-stu-id="2162d-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2162d-362">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2162d-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2162d-363">[Poprzedni: Zacznij](xref:tutorials/razor-pages/razor-pages-start)
> [dalej: Scaffolded Strony brzytwy](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2162d-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
