---
title: Dodawanie modelu do aplikacji Razor Pages w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać klasy do zarządzania filmami w bazie danych przy użyciu entity framework core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658936"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="8dd71-103">Dodawanie modelu do aplikacji Razor Pages w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8dd71-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="8dd71-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8dd71-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="8dd71-105">W tej sekcji klasy są dodawane do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="8dd71-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="8dd71-106">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych SQLite.</span><span class="sxs-lookup"><span data-stu-id="8dd71-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="8dd71-107">Klasy modelu aplikacji są używane z [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="8dd71-108">EF Core jest strukturą mapowania obiekto-relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="8dd71-109">Klasy modelu są znane jako klasy POCO (z "zwykłych starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="8dd71-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8dd71-110">Definiują właściwości danych, które są przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="8dd71-111">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="8dd71-111">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-113">Kliknij prawym przyciskiem myszy projekt **RazorPagesMovie** > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8dd71-114">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-114">Name the folder *Models*.</span></span>

<span data-ttu-id="8dd71-115">Kliknij prawym przyciskiem myszy folder *Modele.*</span><span class="sxs-lookup"><span data-stu-id="8dd71-115">Right click the *Models* folder.</span></span> <span data-ttu-id="8dd71-116">Wybierz **pozycję Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="8dd71-117">Nazwij klasę **Movie**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8dd71-119">Dodawanie folderu o nazwie *Modele*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="8dd71-120">Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-121">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8dd71-122">W witrynie Solution Pad kliknij prawym przyciskiem myszy projekt **RazorPagesMovie,** a następnie wybierz polecenie **Dodaj** > **nowy folder...**. Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="8dd71-123">Kliknij prawym przyciskiem myszy folder *Modele,* a następnie wybierz polecenie **Dodaj** > **nowy plik...**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="8dd71-124">W oknie dialogowym **Nowy plik:**</span><span class="sxs-lookup"><span data-stu-id="8dd71-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8dd71-125">Wybierz **pozycję Ogólne** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="8dd71-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8dd71-126">Wybierz **pozycję Pusta klasa** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="8dd71-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8dd71-127">Nazwij klasę **Film** i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="8dd71-128">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilacji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8dd71-129">Rusztowanie modelu filmu</span><span class="sxs-lookup"><span data-stu-id="8dd71-129">Scaffold the movie model</span></span>

<span data-ttu-id="8dd71-130">W tej sekcji model filmu jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8dd71-131">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-132">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-133">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="8dd71-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8dd71-134">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8dd71-135">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="8dd71-135">Name the folder *Movies*</span></span>

<span data-ttu-id="8dd71-136">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="8dd71-138">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="8dd71-140">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="8dd71-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8dd71-141">W rozwijaniu **klasy Model** wybierz pozycję **Film (RazorPagesMovie.Models).**</span><span class="sxs-lookup"><span data-stu-id="8dd71-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="8dd71-142">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zmień wygenerowaną nazwę z RazorPagesMovie. **Modele**. RazorPagesMovieContext do RazorPagesMovie. **Dane**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8dd71-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="8dd71-143">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="8dd71-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8dd71-144">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="8dd71-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8dd71-145">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-145">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="8dd71-147">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8dd71-149">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="8dd71-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8dd71-150">Zainstaluj narzędzie rusztowania:</span><span class="sxs-lookup"><span data-stu-id="8dd71-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8dd71-151">**W systemie Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8dd71-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8dd71-152">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8dd71-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-153">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8dd71-154">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="8dd71-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8dd71-155">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8dd71-156">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="8dd71-156">Name the folder *Movies*</span></span>

<span data-ttu-id="8dd71-157">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowe rusztowania...**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="8dd71-159">W oknie dialogowym **Nowe rusztowania** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="8dd71-161">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="8dd71-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8dd71-162">W rozwijaniu **klasy Modelu** wybierz lub wpisz **film (RazorPagesMovie.Models).**</span><span class="sxs-lookup"><span data-stu-id="8dd71-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="8dd71-163">W wierszu **klasy Kontekst danych** wpisz nazwę nowej klasy RazorPagesMovie. **Dane**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8dd71-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="8dd71-164">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="8dd71-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8dd71-165">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="8dd71-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8dd71-166">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-166">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="8dd71-168">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="8dd71-169">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="8dd71-169">Add EF tools</span></span>

<span data-ttu-id="8dd71-170">Uruchom następujące polecenie .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="8dd71-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="8dd71-171">Poprzednie polecenie dodaje podstawowe narzędzia entity framework dla interfejsu wiersza polecenia .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8dd71-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="8dd71-172">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="8dd71-172">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-173">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-174">Proces rusztowania tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="8dd71-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8dd71-175">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="8dd71-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8dd71-176">*Dane/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8dd71-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8dd71-177">Zaktualizowano</span><span class="sxs-lookup"><span data-stu-id="8dd71-177">Updated</span></span>

* <span data-ttu-id="8dd71-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8dd71-178">*Startup.cs*</span></span>

<span data-ttu-id="8dd71-179">Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-180">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8dd71-181">Proces rusztowania tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="8dd71-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="8dd71-182">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="8dd71-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8dd71-183">*Dane/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8dd71-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="8dd71-184">Zaktualizowano</span><span class="sxs-lookup"><span data-stu-id="8dd71-184">Updated</span></span>

* <span data-ttu-id="8dd71-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8dd71-185">*Startup.cs*</span></span>

<span data-ttu-id="8dd71-186">Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8dd71-188">Proces rusztowania tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="8dd71-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="8dd71-189">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="8dd71-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="8dd71-190">Utworzone pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8dd71-191">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="8dd71-191">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-192">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-193">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="8dd71-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8dd71-194">Dodaj migrację początkową.</span><span class="sxs-lookup"><span data-stu-id="8dd71-194">Add an initial migration.</span></span>
* <span data-ttu-id="8dd71-195">Zaktualizuj bazę danych za pomocą migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="8dd71-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="8dd71-196">Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package** Manager .</span><span class="sxs-lookup"><span data-stu-id="8dd71-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8dd71-198">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="8dd71-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-200">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="8dd71-201">Poprzednie polecenia generują następujące ostrzeżenie: "Dla kolumny dziesiętnej "Cena" dla kolumny dziesiętnej nie określono żadnego typu "Cena".</span><span class="sxs-lookup"><span data-stu-id="8dd71-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8dd71-202">Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="8dd71-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8dd71-203">Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".</span><span class="sxs-lookup"><span data-stu-id="8dd71-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8dd71-204">Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="8dd71-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="8dd71-205">Polecenie migracji generuje kod do tworzenia początkowego schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="8dd71-206">Schemat jest oparty na modelu `DbContext`określonym w programie .</span><span class="sxs-lookup"><span data-stu-id="8dd71-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="8dd71-207">Argument `InitialCreate` jest używany do nazywania migracji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="8dd71-208">Można użyć dowolnej nazwy, ale zgodnie z konwencją wybrano nazwę opisującą migrację.</span><span class="sxs-lookup"><span data-stu-id="8dd71-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="8dd71-209">Polecenie `update` uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="8dd71-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="8dd71-210">W takim `update` przypadku `Up` uruchamia metodę w *migrations/\<time-stamp>_InitialCreate.cs* file, który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-211">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8dd71-212">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="8dd71-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8dd71-213">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8dd71-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8dd71-214">Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8dd71-215">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="8dd71-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8dd71-216">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="8dd71-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8dd71-217">Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="8dd71-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8dd71-218">Sprawdź `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="8dd71-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8dd71-219">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="8dd71-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="8dd71-220">Współrzędne `RazorPagesMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8dd71-221">Kontekst danych`RazorPagesMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8dd71-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8dd71-222">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="8dd71-223">Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="8dd71-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8dd71-224">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8dd71-225">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="8dd71-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8dd71-226">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8dd71-227">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="8dd71-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8dd71-229">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="8dd71-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-230">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8dd71-231">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="8dd71-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8dd71-232">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="8dd71-232">Test the app</span></span>

* <span data-ttu-id="8dd71-233">Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).</span><span class="sxs-lookup"><span data-stu-id="8dd71-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8dd71-234">Jeśli zostanie wyświetlony błąd:</span><span class="sxs-lookup"><span data-stu-id="8dd71-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8dd71-235">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8dd71-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8dd71-236">Przetestuj łącze **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="8dd71-236">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8dd71-238">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8dd71-239">Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana.</span><span class="sxs-lookup"><span data-stu-id="8dd71-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8dd71-240">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8dd71-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8dd71-241">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8dd71-242">Następny samouczek wyjaśnia pliki utworzone przez szkielety.</span><span class="sxs-lookup"><span data-stu-id="8dd71-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8dd71-243">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8dd71-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8dd71-244">[Poprzedni: Zacznij](xref:tutorials/razor-pages/razor-pages-start)
> [dalej: Scaffolded Strony brzytwy](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="8dd71-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8dd71-245">W tej sekcji klasy są dodawane do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="8dd71-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="8dd71-246">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych SQLite.</span><span class="sxs-lookup"><span data-stu-id="8dd71-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="8dd71-247">Klasy modelu aplikacji są używane z [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="8dd71-248">EF Core jest strukturą mapowania obiekto-relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="8dd71-249">Klasy modelu są znane jako klasy POCO (z "zwykłych starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="8dd71-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8dd71-250">Definiują właściwości danych, które są przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="8dd71-251">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="8dd71-251">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-252">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-253">Kliknij prawym przyciskiem myszy projekt **RazorPagesMovie** > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8dd71-254">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-254">Name the folder *Models*.</span></span>

<span data-ttu-id="8dd71-255">Kliknij prawym przyciskiem myszy folder *Modele.*</span><span class="sxs-lookup"><span data-stu-id="8dd71-255">Right click the *Models* folder.</span></span> <span data-ttu-id="8dd71-256">Wybierz **pozycję Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="8dd71-257">Nazwij klasę **Movie**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8dd71-259">Dodawanie folderu o nazwie *Modele*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="8dd71-260">Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-261">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8dd71-262">W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **RazorPagesMovie,** a następnie wybierz polecenie **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="8dd71-263">Nazwij folder *Models*.</span><span class="sxs-lookup"><span data-stu-id="8dd71-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="8dd71-264">Kliknij prawym przyciskiem myszy folder *Modele,* a następnie wybierz polecenie **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="8dd71-265">W oknie dialogowym **Nowy plik:**</span><span class="sxs-lookup"><span data-stu-id="8dd71-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8dd71-266">Wybierz **pozycję Ogólne** w lewym okienku.</span><span class="sxs-lookup"><span data-stu-id="8dd71-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8dd71-267">Wybierz **pozycję Pusta klasa** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="8dd71-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8dd71-268">Nazwij klasę **Film** i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="8dd71-269">Skompiluj projekt, aby sprawdzić, czy nie ma żadnych błędów kompilacji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8dd71-270">Rusztowanie modelu filmu</span><span class="sxs-lookup"><span data-stu-id="8dd71-270">Scaffold the movie model</span></span>

<span data-ttu-id="8dd71-271">W tej sekcji model filmu jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8dd71-272">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-273">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-274">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="8dd71-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8dd71-275">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8dd71-276">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="8dd71-276">Name the folder *Movies*</span></span>

<span data-ttu-id="8dd71-277">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="8dd71-279">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="8dd71-281">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="8dd71-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="8dd71-282">W rozwijaniu **klasy Model** wybierz pozycję **Film (RazorPagesMovie.Models).**</span><span class="sxs-lookup"><span data-stu-id="8dd71-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="8dd71-283">W wierszu **klasy Kontekst** **+** danych wybierz znak (plus) i zaakceptuj wygenerowaną nazwę **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="8dd71-284">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-284">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="8dd71-286">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8dd71-288">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="8dd71-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8dd71-289">**W systemie Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8dd71-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8dd71-290">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8dd71-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-291">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8dd71-292">Tworzenie folderu *Strony/Filmy:*</span><span class="sxs-lookup"><span data-stu-id="8dd71-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8dd71-293">Kliknij prawym przyciskiem myszy folder *Strony* > **Dodaj** > **nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8dd71-294">Nadawanie nazwy folderowi *Filmy*</span><span class="sxs-lookup"><span data-stu-id="8dd71-294">Name the folder *Movies*</span></span>

<span data-ttu-id="8dd71-295">Kliknij prawym przyciskiem myszy folder *Strony/Filmy* > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="8dd71-297">W oknie dialogowym **Dodawanie nowych rusztowań** wybierz pozycję **Razor Pages using Entity Framework (CRUD)** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="8dd71-299">Wypełnij okno dialogowe **Dodawanie stron razor przy użyciu programu Entity Framework (CRUD):Complete the Add Razor Pages using Entity Framework (CRUD)** dialog:</span><span class="sxs-lookup"><span data-stu-id="8dd71-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8dd71-300">W rozwijaniu **klasy Model** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="8dd71-301">W wierszu **klasy Kontekst danych** wpisz opcję **RazorPagesMovieContext** spowoduje utworzenie nowej klasy kontekstu db z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="8dd71-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="8dd71-302">W tym przypadku będzie to **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="8dd71-303">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-303">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="8dd71-305">Plik *appsettings.json* jest aktualizowany o parametry połączenia używane do łączenia się z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="8dd71-306">Proces rusztowania tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="8dd71-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="8dd71-307">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="8dd71-307">Files created</span></span>

* <span data-ttu-id="8dd71-308">*Strony/filmy:* Tworzenie, usuwanie, Szczegóły, Edycja i Indeks.</span><span class="sxs-lookup"><span data-stu-id="8dd71-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8dd71-309">*Dane/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8dd71-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="8dd71-310">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="8dd71-310">File updated</span></span>

* <span data-ttu-id="8dd71-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8dd71-311">*Startup.cs*</span></span>

<span data-ttu-id="8dd71-312">Utworzone i zaktualizowane pliki są wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8dd71-313">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="8dd71-313">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-314">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd71-315">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="8dd71-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8dd71-316">Dodaj migrację początkową.</span><span class="sxs-lookup"><span data-stu-id="8dd71-316">Add an initial migration.</span></span>
* <span data-ttu-id="8dd71-317">Zaktualizuj bazę danych za pomocą migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="8dd71-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="8dd71-318">Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package** Manager .</span><span class="sxs-lookup"><span data-stu-id="8dd71-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8dd71-320">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="8dd71-320">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="8dd71-321">Polecenie `Add-Migration` generuje kod do tworzenia początkowego schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8dd71-322">Schemat jest oparty na modelu określonym `DbContext` w pliku (W *RazorPagesMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="8dd71-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="8dd71-323">Argument `InitialCreate` jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="8dd71-324">Można użyć dowolnej nazwy, ale zgodnie z konwencją używana jest nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="8dd71-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="8dd71-325">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8dd71-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="8dd71-326">Polecenie `Update-Database` uruchamia `Up` metodę w pliku *Migrations/\<time-stamp>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="8dd71-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="8dd71-327">Metoda `Up` tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-329">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="8dd71-330">Poprzednie polecenia generują następujące ostrzeżenie: "*Dla kolumny dziesiętnej "Cena" dla kolumny dziesiętnej "Cena" dla typu encji "Film". Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".*" Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="8dd71-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd71-331">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd71-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8dd71-332">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="8dd71-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8dd71-333">ASP.NET Core jest zbudowany z [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8dd71-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8dd71-334">Usługi (takie jak kontekst ef core db) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8dd71-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8dd71-335">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="8dd71-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8dd71-336">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="8dd71-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8dd71-337">Narzędzie szkieletu automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="8dd71-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8dd71-338">Sprawdź `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="8dd71-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8dd71-339">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="8dd71-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="8dd71-340">Współrzędne `RazorPagesMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8dd71-341">Kontekst danych`RazorPagesMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8dd71-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8dd71-342">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="8dd71-343">Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="8dd71-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8dd71-344">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8dd71-345">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="8dd71-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8dd71-346">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="8dd71-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8dd71-347">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="8dd71-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8dd71-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8dd71-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8dd71-349">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="8dd71-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8dd71-350">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="8dd71-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8dd71-351">Sprawdź `Up` metodę.</span><span class="sxs-lookup"><span data-stu-id="8dd71-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8dd71-352">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="8dd71-352">Test the app</span></span>

* <span data-ttu-id="8dd71-353">Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).</span><span class="sxs-lookup"><span data-stu-id="8dd71-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8dd71-354">Jeśli zostanie wyświetlony błąd:</span><span class="sxs-lookup"><span data-stu-id="8dd71-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8dd71-355">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8dd71-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8dd71-356">Przetestuj łącze **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="8dd71-356">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8dd71-358">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="8dd71-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8dd71-359">Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana.</span><span class="sxs-lookup"><span data-stu-id="8dd71-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8dd71-360">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8dd71-360">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8dd71-361">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="8dd71-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8dd71-362">Następny samouczek wyjaśnia pliki utworzone przez szkielety.</span><span class="sxs-lookup"><span data-stu-id="8dd71-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8dd71-363">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8dd71-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8dd71-364">[Poprzedni: Zacznij](xref:tutorials/razor-pages/razor-pages-start)
> [dalej: Scaffolded Strony brzytwy](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="8dd71-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
