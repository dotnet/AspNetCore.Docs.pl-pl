---
title: Część 2, Dodawanie modelu
author: rick-anderson
description: 'Część 2 serii samouczków na Razor stronach. W tej sekcji dodawane są klasy modelu.'
ms.author: riande
ms.date: 09/30/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/model
ms.openlocfilehash: ee1b7d77d8d209a11669ba29c8a951c59368180e
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361077"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="9d59e-104">Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d59e-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="9d59e-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d59e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9d59e-106">W tej sekcji są dodawane klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="9d59e-107">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9d59e-108">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="9d59e-109">Najpierw należy napisać klasy modelu, a EF Core utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="9d59e-110">Klasy modelu są znane jako klasy POCO (od " **P** Lain- **O** LD **k** LR **O** biekty"), ponieważ nie mają zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="9d59e-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="9d59e-111">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9d59e-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9d59e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9d59e-113">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9d59e-115">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt *Razor PagesMovie* > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-115">In **Solution Explorer** , right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9d59e-116">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="9d59e-117">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="9d59e-118">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="9d59e-119">Nazwij *film* klasy.</span><span class="sxs-lookup"><span data-stu-id="9d59e-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="9d59e-120">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-121">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-122">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-123">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-124">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-125">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-126">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9d59e-128">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="9d59e-129">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9d59e-130">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-131">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-132">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-133">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-134">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-134">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-135">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-136">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d59e-137">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="9d59e-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d59e-138">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-138">Add a database context class</span></span>

1. <span data-ttu-id="9d59e-139">W projekcie *Razor PagesMovie* Utwórz folder o nazwie *dane*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="9d59e-140">W folderze *dane* Dodaj plik o nazwie *Razor PagesMovieContext.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="9d59e-141">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="9d59e-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d59e-142">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="9d59e-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9d59e-143">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d59e-144">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-144">Add a database connection string</span></span>

<span data-ttu-id="9d59e-145">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d59e-146">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-146">Register the database context</span></span>

1. <span data-ttu-id="9d59e-147">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="9d59e-148">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9d59e-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-149">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9d59e-150">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-150">In the **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="9d59e-151">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="9d59e-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="9d59e-152">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="9d59e-153">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="9d59e-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="9d59e-154">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="9d59e-155">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="9d59e-156">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-157">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-158">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-159">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-160">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-160">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-161">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-162">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9d59e-163">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9d59e-164">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9d59e-165">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="9d59e-165">Scaffold the movie model</span></span>

<span data-ttu-id="9d59e-166">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="9d59e-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9d59e-167">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla Create operacji, odczytu, aktualizacji i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-168">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9d59e-169">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9d59e-170">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9d59e-171">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9d59e-172">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/sca.png)

1. <span data-ttu-id="9d59e-174">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

1. <span data-ttu-id="9d59e-176">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9d59e-177">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="9d59e-178">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="9d59e-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="9d59e-179">W oknie dialogowym **Dodawanie kontekstu danych** nazwa klasy *Razor PagesMovie. Data. Razor PagesMovieContext* jest generowany.</span><span class="sxs-lookup"><span data-stu-id="9d59e-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="9d59e-180">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-180">Select **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="9d59e-182">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9d59e-184">Otwórz powłokę poleceń w katalogu projektu, który zawiera pliki *program.cs* , *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="9d59e-185">**Dla systemu Windows** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9d59e-186">**W przypadku systemów macOS i Linux** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9d59e-187">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcji generatora kodu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="9d59e-188">Opcja</span><span class="sxs-lookup"><span data-stu-id="9d59e-188">Option</span></span>               | <span data-ttu-id="9d59e-189">Opis</span><span class="sxs-lookup"><span data-stu-id="9d59e-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9d59e-190">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9d59e-191">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="9d59e-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9d59e-192">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9d59e-193">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="9d59e-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9d59e-194">Dodaje `_ValidationScriptsPartial` do edycji i Create stron</span><span class="sxs-lookup"><span data-stu-id="9d59e-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9d59e-195">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d59e-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9d59e-196">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9d59e-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d59e-197">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="9d59e-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d59e-198">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="9d59e-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d59e-199">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9d59e-200">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="9d59e-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-201">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9d59e-202">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9d59e-203">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9d59e-204">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9d59e-205">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="9d59e-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

1. <span data-ttu-id="9d59e-207">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="9d59e-209">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9d59e-210">W **klasie DbContext, aby użyć:** Row, nazwij klasę *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="9d59e-211">Wybierz pozycję **Zakończ**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-211">Select **Finish**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/arpMac.png)

<span data-ttu-id="9d59e-213">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d59e-214">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="9d59e-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d59e-215">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="9d59e-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d59e-216">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9d59e-217">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="9d59e-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9d59e-218">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="9d59e-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-220">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d59e-221">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-221">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d59e-222">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d59e-223">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-223">Updated</span></span>

* <span data-ttu-id="9d59e-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-224">*Startup.cs*</span></span>

<span data-ttu-id="9d59e-225">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9d59e-227">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9d59e-228">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-228">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9d59e-229">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-230">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d59e-231">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d59e-232">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-232">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d59e-233">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d59e-234">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-234">Updated</span></span>

* <span data-ttu-id="9d59e-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-235">*Startup.cs*</span></span>

<span data-ttu-id="9d59e-236">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="9d59e-237">Create początkowy schemat bazy danych przy użyciu funkcji migracji EF</span><span class="sxs-lookup"><span data-stu-id="9d59e-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="9d59e-238">Funkcja migracji w Entity Framework Core zapewnia sposób:</span><span class="sxs-lookup"><span data-stu-id="9d59e-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="9d59e-239">Create początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-239">Create the initial database schema.</span></span>
* <span data-ttu-id="9d59e-240">Uaktualnij przyrostowo schemat bazy danych, aby zachować synchronizację z modelem danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="9d59e-241">Istniejące dane w bazie danych są zachowywane.</span><span class="sxs-lookup"><span data-stu-id="9d59e-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-242">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-243">W tej sekcji okno **konsoli Menedżera pakietów** (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="9d59e-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="9d59e-244">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-244">Add an initial migration.</span></span>
* <span data-ttu-id="9d59e-245">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="9d59e-246">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="9d59e-248">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d59e-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-249">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="9d59e-250">Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="9d59e-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="9d59e-251">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="9d59e-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9d59e-252">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="9d59e-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9d59e-253">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="9d59e-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9d59e-254">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9d59e-255">`migrations`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9d59e-256">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9d59e-257">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9d59e-258">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="9d59e-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9d59e-259">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="9d59e-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9d59e-260">W takim przypadku program `update` uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _Initial Create . cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-261">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9d59e-262">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="9d59e-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9d59e-263">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9d59e-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d59e-264">Usługi, takie jak kontekst bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d59e-265">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="9d59e-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d59e-266">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="9d59e-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9d59e-267">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="9d59e-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9d59e-268">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="9d59e-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9d59e-269">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="9d59e-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9d59e-270">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak Create , Odczyt, aktualizacja i Delete , dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9d59e-271">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9d59e-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9d59e-272">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d59e-273">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="9d59e-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="9d59e-274">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9d59e-275">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="9d59e-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d59e-276">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="9d59e-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9d59e-277">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-278">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9d59e-279">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="9d59e-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="9d59e-280">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-280">Test the app</span></span>

1. <span data-ttu-id="9d59e-281">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="9d59e-282">Jeśli pojawi się następujący komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="9d59e-283">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9d59e-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="9d59e-284">Przetestuj **Create** link.</span><span class="sxs-lookup"><span data-stu-id="9d59e-284">Test the **Create** link.</span></span>

   ![::: No-Loc (Create)::: Page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="9d59e-286">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9d59e-287">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="9d59e-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9d59e-288">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="9d59e-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="9d59e-289">Przetestuj **Edytowanie** , **szczegóły** i **Delete** linki.</span><span class="sxs-lookup"><span data-stu-id="9d59e-289">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="9d59e-290">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d59e-291">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9d59e-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d59e-292">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9d59e-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9d59e-293">W tej sekcji dodano klasy do zarządzania filmami.</span><span class="sxs-lookup"><span data-stu-id="9d59e-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="9d59e-294">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9d59e-295">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="9d59e-296">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="9d59e-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9d59e-297">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9d59e-298">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9d59e-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9d59e-299">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-300">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-301">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9d59e-302">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-302">Name the folder *Models*.</span></span>

<span data-ttu-id="9d59e-303">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="9d59e-304">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="9d59e-305">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="9d59e-305">Name the class **Movie**.</span></span>

<span data-ttu-id="9d59e-306">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-307">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-308">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-309">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-310">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-310">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-311">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-312">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d59e-313">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9d59e-315">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9d59e-316">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9d59e-317">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-318">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-319">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-320">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-321">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-321">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-322">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-323">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d59e-324">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d59e-325">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="9d59e-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d59e-326">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-326">Add a database context class</span></span>

* <span data-ttu-id="9d59e-327">W projekcie *Razor PagesMovie* Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="9d59e-328">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d59e-329">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="9d59e-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d59e-330">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="9d59e-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9d59e-331">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d59e-332">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-332">Add a database connection string</span></span>

<span data-ttu-id="9d59e-333">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d59e-334">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-334">Register the database context</span></span>

<span data-ttu-id="9d59e-335">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9d59e-336">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-337">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9d59e-338">W **oknie narzędzia rozwiązania** kliknij projekt **Razor PagesMovie** , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-338">In the **Solution Tool Window** , control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="9d59e-339">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="9d59e-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="9d59e-340">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9d59e-341">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="9d59e-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9d59e-342">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9d59e-343">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9d59e-344">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-345">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-346">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-347">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-348">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-348">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-349">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-350">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9d59e-351">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9d59e-352">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9d59e-353">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="9d59e-353">Scaffold the movie model</span></span>

<span data-ttu-id="9d59e-354">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="9d59e-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9d59e-355">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla Create operacji, odczytu, aktualizacji i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-356">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-357">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d59e-358">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d59e-359">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d59e-360">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="9d59e-362">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="9d59e-364">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9d59e-365">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9d59e-366">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę z Razor PagesMovie. **Modele**. Razor PagesMovieContext do Razor PagesMovie. **Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9d59e-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9d59e-367">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="9d59e-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9d59e-368">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="9d59e-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9d59e-369">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-369">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="9d59e-371">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9d59e-373">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs* , *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="9d59e-374">**Dla systemu Windows** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9d59e-375">**W przypadku systemów macOS i Linux** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9d59e-376">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="9d59e-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9d59e-377">Opcja</span><span class="sxs-lookup"><span data-stu-id="9d59e-377">Option</span></span>               | <span data-ttu-id="9d59e-378">Opis</span><span class="sxs-lookup"><span data-stu-id="9d59e-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9d59e-379">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9d59e-380">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="9d59e-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9d59e-381">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9d59e-382">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="9d59e-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9d59e-383">Dodaje `_ValidationScriptsPartial` do edycji i Create stron</span><span class="sxs-lookup"><span data-stu-id="9d59e-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9d59e-384">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d59e-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9d59e-385">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9d59e-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d59e-386">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="9d59e-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d59e-387">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="9d59e-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d59e-388">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="9d59e-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9d59e-389">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="9d59e-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-390">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d59e-391">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d59e-392">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d59e-393">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d59e-394">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="9d59e-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="9d59e-396">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9d59e-398">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9d59e-399">W menu rozwijanym **Klasa modelu** wybierz lub wpisz **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9d59e-400">W wierszu **klasy kontekstu danych** wpisz nazwę nowej klasy, Razor PagesMovie. **Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9d59e-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9d59e-401">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="9d59e-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9d59e-402">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="9d59e-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9d59e-403">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-403">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="9d59e-405">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="9d59e-406">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="9d59e-406">Add EF tools</span></span>

<span data-ttu-id="9d59e-407">Uruchom następujące interfejs wiersza polecenia platformy .NET Core polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="9d59e-408">Poprzednie polecenie dodaje Entity Framework Core narzędzia dla interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9d59e-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="9d59e-409">Aby uzyskać więcej informacji, zobacz [Entity Framework Core Tools Reference-interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="9d59e-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d59e-410">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="9d59e-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d59e-411">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="9d59e-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d59e-412">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="9d59e-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9d59e-413">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="9d59e-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9d59e-414">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="9d59e-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-415">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-416">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d59e-417">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-417">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d59e-418">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d59e-419">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-419">Updated</span></span>

* <span data-ttu-id="9d59e-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-420">*Startup.cs*</span></span>

<span data-ttu-id="9d59e-421">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-422">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d59e-423">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d59e-424">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-424">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d59e-425">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d59e-426">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-426">Updated</span></span>

* <span data-ttu-id="9d59e-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-427">*Startup.cs*</span></span>

<span data-ttu-id="9d59e-428">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9d59e-430">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9d59e-431">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-431">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9d59e-432">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9d59e-433">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="9d59e-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-434">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-435">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="9d59e-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9d59e-436">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-436">Add an initial migration.</span></span>
* <span data-ttu-id="9d59e-437">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="9d59e-438">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9d59e-440">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d59e-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-441">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9d59e-442">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="9d59e-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9d59e-443">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="9d59e-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9d59e-444">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="9d59e-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9d59e-445">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="9d59e-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9d59e-446">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9d59e-447">Polecenie migrations generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="9d59e-448">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9d59e-449">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9d59e-450">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="9d59e-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9d59e-451">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="9d59e-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9d59e-452">W takim przypadku program `update` uruchamia `Up` metodę w pliku  *migrations/ \<time-stamp> _Initial Create . cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-453">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9d59e-454">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="9d59e-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9d59e-455">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9d59e-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d59e-456">Usługi, takie jak kontekst kontekstu bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d59e-457">Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="9d59e-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d59e-458">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="9d59e-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9d59e-459">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="9d59e-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9d59e-460">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="9d59e-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9d59e-461">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="9d59e-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9d59e-462">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak Create , Odczyt, aktualizacja i Delete , dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9d59e-463">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9d59e-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9d59e-464">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d59e-465">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="9d59e-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9d59e-466">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9d59e-467">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="9d59e-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d59e-468">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="9d59e-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9d59e-469">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-470">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9d59e-471">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="9d59e-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9d59e-472">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-472">Test the app</span></span>

* <span data-ttu-id="9d59e-473">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9d59e-474">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="9d59e-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9d59e-475">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9d59e-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9d59e-476">Przetestuj **Create** link.</span><span class="sxs-lookup"><span data-stu-id="9d59e-476">Test the **Create** link.</span></span>

  ![::: No-Loc (Create)::: Page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="9d59e-478">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9d59e-479">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="9d59e-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9d59e-480">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="9d59e-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9d59e-481">Przetestuj **Edytowanie** , **szczegóły** i **Delete** linki.</span><span class="sxs-lookup"><span data-stu-id="9d59e-481">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="9d59e-482">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d59e-483">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9d59e-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d59e-484">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9d59e-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d59e-485">W tej sekcji są dodawane klasy do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9d59e-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="9d59e-486">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="9d59e-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="9d59e-487">Klasy modelu aplikacji są używane z [Entity Framework Core (Ef Core)](/ef/core) ([dostawca bazy danych programu SQLite EF Core](/ef/core/providers/sqlite)) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="9d59e-488">EF Core to struktura obiektów mapowania relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="9d59e-489">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="9d59e-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9d59e-490">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9d59e-491">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9d59e-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9d59e-492">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-493">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-494">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9d59e-495">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-495">Name the folder *Models*.</span></span>

<span data-ttu-id="9d59e-496">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="9d59e-497">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="9d59e-498">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="9d59e-498">Name the class **Movie**.</span></span>

<span data-ttu-id="9d59e-499">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-500">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-501">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-502">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-503">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-503">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-504">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-505">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d59e-506">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9d59e-508">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9d59e-509">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9d59e-510">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-511">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-512">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-513">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-514">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-514">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-515">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-516">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d59e-517">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d59e-518">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="9d59e-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d59e-519">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-519">Add a database context class</span></span>

<span data-ttu-id="9d59e-520">W Razor projekcie PagesMovie Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="9d59e-521">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d59e-522">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="9d59e-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d59e-523">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="9d59e-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9d59e-524">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d59e-525">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-525">Add a database connection string</span></span>

<span data-ttu-id="9d59e-526">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="9d59e-527">Dodaj wymagane pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="9d59e-527">Add required NuGet packages</span></span>

<span data-ttu-id="9d59e-528">Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="9d59e-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="9d59e-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d59e-530">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="9d59e-530">Register the database context</span></span>

<span data-ttu-id="9d59e-531">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9d59e-532">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="9d59e-533">Kompiluj projekt jako sprawdzenie pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-534">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9d59e-535">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-535">In **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="9d59e-536">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="9d59e-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="9d59e-537">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="9d59e-538">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9d59e-539">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="9d59e-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9d59e-540">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9d59e-541">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9d59e-542">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="9d59e-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d59e-543">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="9d59e-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d59e-544">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d59e-545">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d59e-546">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="9d59e-546">With this attribute:</span></span>

  * <span data-ttu-id="9d59e-547">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="9d59e-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d59e-548">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="9d59e-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d59e-549">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="9d59e-550">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9d59e-551">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="9d59e-551">Scaffold the movie model</span></span>

<span data-ttu-id="9d59e-552">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="9d59e-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9d59e-553">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla Create operacji, odczytu, aktualizacji i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-554">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-555">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d59e-556">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d59e-557">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d59e-558">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="9d59e-560">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="9d59e-562">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="9d59e-563">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9d59e-564">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zaakceptuj wygenerowaną nazwę **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9d59e-565">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-565">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="9d59e-567">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d59e-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d59e-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9d59e-569">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs* , *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="9d59e-570">**Dla systemu Windows** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9d59e-571">**W przypadku systemów macOS i Linux** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9d59e-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9d59e-572">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="9d59e-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9d59e-573">Opcja</span><span class="sxs-lookup"><span data-stu-id="9d59e-573">Option</span></span>               | <span data-ttu-id="9d59e-574">Opis</span><span class="sxs-lookup"><span data-stu-id="9d59e-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9d59e-575">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9d59e-576">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="9d59e-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9d59e-577">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="9d59e-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9d59e-578">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="9d59e-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9d59e-579">Dodaje `_ValidationScriptsPartial` do edycji i Create stron</span><span class="sxs-lookup"><span data-stu-id="9d59e-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9d59e-580">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d59e-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9d59e-581">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9d59e-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-582">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d59e-583">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="9d59e-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d59e-584">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d59e-585">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="9d59e-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d59e-586">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="9d59e-588">W oknie dialogowym **Dodawanie nowej szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9d59e-590">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="9d59e-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9d59e-591">Z listy rozwijanej **Klasa modelu** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="9d59e-592">W wierszu **klasy kontekstu danych** wpisz wybierz **Razor PagesMovieContext** to spowoduje utworzenie nowej klasy kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="9d59e-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="9d59e-593">W takim przypadku będzie to **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9d59e-594">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-594">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="9d59e-596">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="9d59e-597">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="9d59e-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="9d59e-598">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="9d59e-598">Files created</span></span>

* <span data-ttu-id="9d59e-599">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="9d59e-599">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d59e-600">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="9d59e-601">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="9d59e-601">File updated</span></span>

* <span data-ttu-id="9d59e-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d59e-602">*Startup.cs*</span></span>

<span data-ttu-id="9d59e-603">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9d59e-604">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="9d59e-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-605">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d59e-606">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="9d59e-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9d59e-607">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-607">Add an initial migration.</span></span>
* <span data-ttu-id="9d59e-608">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="9d59e-609">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="9d59e-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9d59e-611">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="9d59e-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="9d59e-612">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9d59e-613">Schemat jest oparty na modelu określonym w `DbContext` , w pliku *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="9d59e-614">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="9d59e-615">Można użyć dowolnej nazwy, ale według Konwencji Nazwa opisująca migrację jest używana.</span><span class="sxs-lookup"><span data-stu-id="9d59e-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="9d59e-616">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="9d59e-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="9d59e-617">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _Initial Create . cs* .</span><span class="sxs-lookup"><span data-stu-id="9d59e-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="9d59e-618">`Up`Metoda tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-619">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9d59e-620">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="9d59e-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9d59e-621">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="9d59e-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9d59e-622">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="9d59e-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9d59e-623">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="9d59e-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9d59e-624">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d59e-625">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d59e-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9d59e-626">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="9d59e-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9d59e-627">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9d59e-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d59e-628">Usługi (takie jak kontekst kontekstu bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9d59e-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d59e-629">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="9d59e-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d59e-630">Kod konstruktora, który pobiera wystąpienie kontekstu contextB kontekstu bazy danych jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="9d59e-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9d59e-631">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="9d59e-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9d59e-632">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="9d59e-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9d59e-633">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="9d59e-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="9d59e-634">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak Create , Odczyt, aktualizacja i Delete , dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9d59e-635">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="9d59e-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9d59e-636">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d59e-637">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="9d59e-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9d59e-638">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9d59e-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9d59e-639">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="9d59e-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d59e-640">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="9d59e-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9d59e-641">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="9d59e-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d59e-642">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9d59e-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9d59e-643">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="9d59e-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9d59e-644">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="9d59e-644">Test the app</span></span>

* <span data-ttu-id="9d59e-645">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="9d59e-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9d59e-646">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="9d59e-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9d59e-647">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="9d59e-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9d59e-648">Przetestuj **Create** link.</span><span class="sxs-lookup"><span data-stu-id="9d59e-648">Test the **Create** link.</span></span>

  ![::: No-Loc (Create)::: Page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="9d59e-650">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="9d59e-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9d59e-651">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="9d59e-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9d59e-652">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="9d59e-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9d59e-653">Przetestuj **Edytowanie** , **szczegóły** i **Delete** linki.</span><span class="sxs-lookup"><span data-stu-id="9d59e-653">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="9d59e-654">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="9d59e-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d59e-655">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9d59e-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d59e-656">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9d59e-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
