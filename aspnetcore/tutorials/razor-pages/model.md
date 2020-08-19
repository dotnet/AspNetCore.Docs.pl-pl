---
title: Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core
author: rick-anderson
description: Część 2 serii samouczków na Razor stronach.
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4099873142b99afb7f0659dfd9a4fde8bec3081d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633776"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="2834f-103">Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2834f-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2834f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2834f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2834f-105">W tej sekcji dodano klasy do zarządzania filmami.</span><span class="sxs-lookup"><span data-stu-id="2834f-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="2834f-106">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2834f-107">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="2834f-108">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="2834f-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2834f-109">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="2834f-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2834f-110">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="2834f-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-112">Kliknij prawym przyciskiem myszy projekt \*\* Razor PagesMovie\*\* > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2834f-113">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="2834f-113">Name the folder *Models*.</span></span>

<span data-ttu-id="2834f-114">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="2834f-114">Right click the *Models* folder.</span></span> <span data-ttu-id="2834f-115">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="2834f-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="2834f-116">Nazwij **film**klasy.</span><span class="sxs-lookup"><span data-stu-id="2834f-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2834f-118">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="2834f-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2834f-119">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2834f-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-120">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2834f-121">W okienko rozwiązania kliknij prawym przyciskiem myszy projekt \*\* Razor PagesMovie\*\* , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="2834f-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="2834f-122">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="2834f-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="2834f-123">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="2834f-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2834f-124">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="2834f-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2834f-125">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="2834f-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2834f-126">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="2834f-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2834f-127">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="2834f-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2834f-128">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="2834f-128">Scaffold the movie model</span></span>

<span data-ttu-id="2834f-129">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="2834f-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2834f-130">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="2834f-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-132">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="2834f-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2834f-133">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2834f-134">Nazwij foldery *filmów*</span><span class="sxs-lookup"><span data-stu-id="2834f-134">Name the folder *Movies*</span></span>

<span data-ttu-id="2834f-135">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2834f-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="2834f-137">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="2834f-139">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2834f-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2834f-140">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="2834f-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2834f-141">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę z Razor PagesMovie.\*\* Modele**. Razor PagesMovieContext do Razor PagesMovie.** Dane\*\*. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2834f-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2834f-142">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="2834f-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2834f-143">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="2834f-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2834f-144">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-144">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="2834f-146">*appsettings.jsw* pliku jest aktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2834f-148">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs*i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="2834f-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2834f-149">Zainstaluj narzędzie do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2834f-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="2834f-150">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2834f-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2834f-151">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2834f-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-152">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2834f-153">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="2834f-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2834f-154">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2834f-155">Nazwij foldery *filmów*</span><span class="sxs-lookup"><span data-stu-id="2834f-155">Name the folder *Movies*</span></span>

<span data-ttu-id="2834f-156">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="2834f-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="2834f-158">W oknie dialogowym **Nowa rusztowania** wybierz pozycję \*\* Razor strony z Entity Framework (CRUD)\*\* > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="2834f-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2834f-160">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2834f-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2834f-161">W menu rozwijanym **Klasa modelu** wybierz lub wpisz **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="2834f-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2834f-162">W wierszu **klasy kontekstu danych** wpisz nazwę nowej klasy, Razor PagesMovie.\*\* Dane\*\*. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2834f-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2834f-163">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="2834f-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2834f-164">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="2834f-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2834f-165">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-165">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="2834f-167">*appsettings.jsw* pliku jest aktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="2834f-168">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="2834f-168">Add EF tools</span></span>

<span data-ttu-id="2834f-169">Uruchom następujące interfejs wiersza polecenia platformy .NET Core polecenie:</span><span class="sxs-lookup"><span data-stu-id="2834f-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="2834f-170">Poprzednie polecenie dodaje Entity Framework Core narzędzia dla interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2834f-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="2834f-171">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="2834f-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-173">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2834f-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2834f-174">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="2834f-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2834f-175">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2834f-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2834f-176">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="2834f-176">Updated</span></span>

* <span data-ttu-id="2834f-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2834f-177">*Startup.cs*</span></span>

<span data-ttu-id="2834f-178">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2834f-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-179">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2834f-180">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2834f-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2834f-181">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="2834f-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2834f-182">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2834f-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2834f-183">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="2834f-183">Updated</span></span>

* <span data-ttu-id="2834f-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2834f-184">*Startup.cs*</span></span>

<span data-ttu-id="2834f-185">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2834f-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2834f-187">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2834f-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2834f-188">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="2834f-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2834f-189">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2834f-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2834f-190">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="2834f-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-192">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="2834f-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2834f-193">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="2834f-193">Add an initial migration.</span></span>
* <span data-ttu-id="2834f-194">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="2834f-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="2834f-195">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="2834f-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2834f-197">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2834f-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-199">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="2834f-200">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="2834f-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2834f-201">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="2834f-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2834f-202">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="2834f-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2834f-203">Możesz zignorować to ostrzeżenie. zostanie on rozwiązany w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="2834f-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="2834f-204">Polecenie migrations generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="2834f-205">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2834f-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2834f-206">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="2834f-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2834f-207">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="2834f-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2834f-208">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="2834f-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2834f-209">W takim przypadku program `update` uruchamia `Up` metodę w pliku  *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2834f-211">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="2834f-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2834f-212">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2834f-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2834f-213">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2834f-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2834f-214">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2834f-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2834f-215">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="2834f-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2834f-216">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2834f-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2834f-217">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="2834f-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2834f-218">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2834f-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2834f-219">`RazorPagesMovieContext`Współrzędne EF Core funkcje (tworzenie, Odczyt, aktualizowanie, usuwanie itp.) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="2834f-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2834f-220">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2834f-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2834f-221">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2834f-222">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2834f-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2834f-223">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2834f-224">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2834f-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2834f-225">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="2834f-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2834f-226">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="2834f-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2834f-228">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="2834f-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-229">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2834f-230">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="2834f-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2834f-231">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2834f-231">Test the app</span></span>

* <span data-ttu-id="2834f-232">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="2834f-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2834f-233">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="2834f-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2834f-234">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2834f-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2834f-235">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="2834f-235">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2834f-237">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="2834f-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2834f-238">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów dat innych niż angielski, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="2834f-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2834f-239">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="2834f-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2834f-240">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="2834f-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2834f-241">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="2834f-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2834f-242">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2834f-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2834f-243">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2834f-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2834f-244">W tej sekcji są dodawane klasy do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2834f-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2834f-245">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="2834f-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2834f-246">Klasy modelu aplikacji są używane z [Entity Framework Core (Ef Core)](/ef/core) ([dostawca bazy danych programu SQLite EF Core](/ef/core/providers/sqlite)) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2834f-247">EF Core to struktura obiektów mapowania relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2834f-248">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="2834f-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2834f-249">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="2834f-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2834f-250">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="2834f-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-252">Kliknij prawym przyciskiem myszy projekt \*\* Razor PagesMovie\*\* > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2834f-253">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="2834f-253">Name the folder *Models*.</span></span>

<span data-ttu-id="2834f-254">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="2834f-254">Right click the *Models* folder.</span></span> <span data-ttu-id="2834f-255">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="2834f-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="2834f-256">Nazwij **film**klasy.</span><span class="sxs-lookup"><span data-stu-id="2834f-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2834f-258">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="2834f-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2834f-259">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2834f-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-260">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2834f-261">W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt \*\* Razor PagesMovie\*\* , a następnie wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2834f-262">Nazwij *modele*folderów.</span><span class="sxs-lookup"><span data-stu-id="2834f-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="2834f-263">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="2834f-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2834f-264">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="2834f-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2834f-265">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="2834f-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2834f-266">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="2834f-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2834f-267">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="2834f-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2834f-268">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="2834f-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2834f-269">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="2834f-269">Scaffold the movie model</span></span>

<span data-ttu-id="2834f-270">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="2834f-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2834f-271">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="2834f-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-273">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="2834f-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2834f-274">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2834f-275">Nazwij foldery *filmów*</span><span class="sxs-lookup"><span data-stu-id="2834f-275">Name the folder *Movies*</span></span>

<span data-ttu-id="2834f-276">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2834f-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="2834f-278">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="2834f-280">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2834f-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="2834f-281">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="2834f-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2834f-282">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zaakceptuj wygenerowaną nazwę \*\* Razor PagesMovie. models. Razor PagesMovieContext\*\*.</span><span class="sxs-lookup"><span data-stu-id="2834f-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2834f-283">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-283">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="2834f-285">*appsettings.jsw* pliku jest aktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2834f-287">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs*i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="2834f-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="2834f-288">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2834f-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2834f-289">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2834f-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-290">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2834f-291">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="2834f-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2834f-292">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="2834f-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2834f-293">Nazwij foldery *filmów*</span><span class="sxs-lookup"><span data-stu-id="2834f-293">Name the folder *Movies*</span></span>

<span data-ttu-id="2834f-294">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2834f-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="2834f-296">W oknie dialogowym **Dodawanie nowej szkieletu** wybierz pozycję \*\* Razor strony z użyciem Entity Framework (CRUD)\*\* > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2834f-298">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="2834f-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2834f-299">Z listy rozwijanej **Klasa modelu** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="2834f-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="2834f-300">W wierszu **klasy kontekstu danych** wpisz wybierz \*\* Razor PagesMovieContext\*\* to spowoduje utworzenie nowej klasy kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="2834f-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="2834f-301">W takim przypadku będzie to \*\* Razor PagesMovie. models. Razor PagesMovieContext\*\*.</span><span class="sxs-lookup"><span data-stu-id="2834f-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2834f-302">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2834f-302">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="2834f-304">*appsettings.jsw* pliku jest aktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="2834f-305">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="2834f-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2834f-306">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="2834f-306">Files created</span></span>

* <span data-ttu-id="2834f-307">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="2834f-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2834f-308">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2834f-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2834f-309">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="2834f-309">File updated</span></span>

* <span data-ttu-id="2834f-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2834f-310">*Startup.cs*</span></span>

<span data-ttu-id="2834f-311">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="2834f-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2834f-312">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="2834f-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2834f-314">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="2834f-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2834f-315">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="2834f-315">Add an initial migration.</span></span>
* <span data-ttu-id="2834f-316">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="2834f-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="2834f-317">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="2834f-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2834f-319">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2834f-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2834f-320">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2834f-321">Schemat jest oparty na modelu określonym w `DbContext` (w pliku \* Razor PagesMovieContext.cs\* ).</span><span class="sxs-lookup"><span data-stu-id="2834f-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2834f-322">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="2834f-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="2834f-323">Można użyć dowolnej nazwy, ale według Konwencji Nazwa opisująca migrację jest używana.</span><span class="sxs-lookup"><span data-stu-id="2834f-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2834f-324">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="2834f-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="2834f-325">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="2834f-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2834f-326">`Up`Metoda tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-328">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="2834f-329">Powyższe polecenia generują następujące ostrzeżenie: "*nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ". Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali. Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".* Możesz zignorować to ostrzeżenie. zostanie on rozwiązany w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="2834f-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2834f-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2834f-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2834f-331">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="2834f-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2834f-332">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2834f-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2834f-333">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2834f-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2834f-334">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="2834f-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2834f-335">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="2834f-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2834f-336">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="2834f-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2834f-337">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="2834f-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2834f-338">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2834f-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2834f-339">`RazorPagesMovieContext`Współrzędne EF Core funkcje (tworzenie, Odczyt, aktualizowanie, usuwanie itp.) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="2834f-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2834f-340">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2834f-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2834f-341">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2834f-342">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="2834f-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2834f-343">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2834f-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2834f-344">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="2834f-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2834f-345">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="2834f-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2834f-346">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="2834f-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2834f-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2834f-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2834f-348">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="2834f-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2834f-349">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2834f-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2834f-350">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="2834f-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2834f-351">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2834f-351">Test the app</span></span>

* <span data-ttu-id="2834f-352">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="2834f-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2834f-353">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="2834f-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2834f-354">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2834f-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2834f-355">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="2834f-355">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2834f-357">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="2834f-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2834f-358">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów dat innych niż angielski, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="2834f-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2834f-359">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="2834f-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2834f-360">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="2834f-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2834f-361">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="2834f-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2834f-362">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2834f-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2834f-363">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2834f-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
