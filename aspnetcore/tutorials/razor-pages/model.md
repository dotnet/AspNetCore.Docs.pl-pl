---
title: Część 2, Dodawanie modelu
author: rick-anderson
description: 'Część 2 serii samouczków na Razor stronach. W tej sekcji dodawane są klasy modelu.'
ms.author: riande
ms.date: 11/11/2020
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
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550697"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="3ea32-104">Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ea32-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="3ea32-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3ea32-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="3ea32-106">W tej sekcji są dodawane klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="3ea32-107">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="3ea32-108">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="3ea32-109">Najpierw należy napisać klasy modelu, a EF Core utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="3ea32-110">Klasy modelu są znane jako klasy POCO (od " **P** Lain- **O** LD **k** LR **O** biekty"), ponieważ nie mają zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="3ea32-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="3ea32-111">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3ea32-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3ea32-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3ea32-113">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3ea32-115">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt *Razor PagesMovie* > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-115">In **Solution Explorer** , right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3ea32-116">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="3ea32-117">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="3ea32-118">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="3ea32-119">Nazwij *film* klasy.</span><span class="sxs-lookup"><span data-stu-id="3ea32-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="3ea32-120">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-121">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-122">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-123">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-124">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-125">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-126">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3ea32-128">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="3ea32-129">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3ea32-130">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-131">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-132">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-133">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-134">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-134">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-135">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-136">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3ea32-137">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="3ea32-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3ea32-138">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-138">Add a database context class</span></span>

1. <span data-ttu-id="3ea32-139">W projekcie *Razor PagesMovie* Utwórz folder o nazwie *dane*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="3ea32-140">W folderze *dane* Dodaj plik o nazwie *Razor PagesMovieContext.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="3ea32-141">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="3ea32-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3ea32-142">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="3ea32-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3ea32-143">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3ea32-144">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-144">Add a database connection string</span></span>

<span data-ttu-id="3ea32-145">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3ea32-146">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-146">Register the database context</span></span>

1. <span data-ttu-id="3ea32-147">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="3ea32-148">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3ea32-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-149">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3ea32-150">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-150">In the **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="3ea32-151">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="3ea32-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="3ea32-152">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="3ea32-153">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="3ea32-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="3ea32-154">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="3ea32-155">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="3ea32-156">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-157">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-158">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-159">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-160">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-160">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-161">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-162">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="3ea32-163">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="3ea32-164">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3ea32-165">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="3ea32-165">Scaffold the movie model</span></span>

<span data-ttu-id="3ea32-166">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="3ea32-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3ea32-167">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla Create operacji, odczytu, aktualizacji i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-168">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3ea32-169">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="3ea32-170">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="3ea32-171">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="3ea32-172">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/sca.png)

1. <span data-ttu-id="3ea32-174">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

1. <span data-ttu-id="3ea32-176">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="3ea32-177">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="3ea32-178">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="3ea32-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="3ea32-179">W oknie dialogowym **Dodawanie kontekstu danych** nazwa klasy *Razor PagesMovie. Data. Razor PagesMovieContext* jest generowany.</span><span class="sxs-lookup"><span data-stu-id="3ea32-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="3ea32-180">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-180">Select **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="3ea32-182">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3ea32-184">Otwórz powłokę poleceń w katalogu projektu, który zawiera pliki *program.cs* , *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="3ea32-185">**Dla systemu Windows** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3ea32-186">**W przypadku systemów macOS i Linux** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3ea32-187">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcji generatora kodu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="3ea32-188">Opcja</span><span class="sxs-lookup"><span data-stu-id="3ea32-188">Option</span></span>               | <span data-ttu-id="3ea32-189">Opis</span><span class="sxs-lookup"><span data-stu-id="3ea32-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3ea32-190">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3ea32-191">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="3ea32-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3ea32-192">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3ea32-193">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="3ea32-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3ea32-194">Dodaje `_ValidationScriptsPartial` do edycji i Create stron</span><span class="sxs-lookup"><span data-stu-id="3ea32-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3ea32-195">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="3ea32-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3ea32-196">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="3ea32-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ea32-197">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="3ea32-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ea32-198">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="3ea32-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ea32-199">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="3ea32-200">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3ea32-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-201">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3ea32-202">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="3ea32-203">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="3ea32-204">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="3ea32-205">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="3ea32-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

1. <span data-ttu-id="3ea32-207">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="3ea32-209">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="3ea32-210">W **klasie DbContext, aby użyć:** Row, nazwij klasę *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="3ea32-211">Wybierz pozycję **Zakończ**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-211">Select **Finish**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/arpMac.png)

<span data-ttu-id="3ea32-213">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ea32-214">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="3ea32-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ea32-215">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="3ea32-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ea32-216">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="3ea32-217">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3ea32-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="3ea32-218">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="3ea32-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-220">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ea32-221">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-221">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ea32-222">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ea32-223">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-223">Updated</span></span>

* <span data-ttu-id="3ea32-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-224">*Startup.cs*</span></span>

<span data-ttu-id="3ea32-225">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3ea32-227">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="3ea32-228">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-228">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="3ea32-229">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-230">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ea32-231">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ea32-232">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-232">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ea32-233">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ea32-234">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-234">Updated</span></span>

* <span data-ttu-id="3ea32-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-235">*Startup.cs*</span></span>

<span data-ttu-id="3ea32-236">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="3ea32-237">Create początkowy schemat bazy danych przy użyciu funkcji migracji EF</span><span class="sxs-lookup"><span data-stu-id="3ea32-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="3ea32-238">Funkcja migracji w Entity Framework Core zapewnia sposób:</span><span class="sxs-lookup"><span data-stu-id="3ea32-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="3ea32-239">Create początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-239">Create the initial database schema.</span></span>
* <span data-ttu-id="3ea32-240">Uaktualnij przyrostowo schemat bazy danych, aby zachować synchronizację z modelem danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="3ea32-241">Istniejące dane w bazie danych są zachowywane.</span><span class="sxs-lookup"><span data-stu-id="3ea32-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-242">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-243">W tej sekcji okno **konsoli Menedżera pakietów** (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="3ea32-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="3ea32-244">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-244">Add an initial migration.</span></span>
* <span data-ttu-id="3ea32-245">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="3ea32-246">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="3ea32-248">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3ea32-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-249">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="3ea32-250">Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="3ea32-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="3ea32-251">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="3ea32-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3ea32-252">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="3ea32-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3ea32-253">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="3ea32-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3ea32-254">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="3ea32-255">`migrations`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3ea32-256">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="3ea32-257">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3ea32-258">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="3ea32-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3ea32-259">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="3ea32-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="3ea32-260">W takim przypadku program `update` uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _Initial Create . cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-261">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3ea32-262">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="3ea32-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3ea32-263">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3ea32-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3ea32-264">Usługi, takie jak kontekst bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3ea32-265">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="3ea32-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3ea32-266">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="3ea32-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3ea32-267">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="3ea32-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3ea32-268">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="3ea32-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3ea32-269">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="3ea32-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3ea32-270">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak Create , Odczyt, aktualizacja i Delete , dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3ea32-271">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="3ea32-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3ea32-272">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ea32-273">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="3ea32-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="3ea32-274">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3ea32-275">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="3ea32-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3ea32-276">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="3ea32-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3ea32-277">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-278">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3ea32-279">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="3ea32-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="3ea32-280">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-280">Test the app</span></span>

1. <span data-ttu-id="3ea32-281">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="3ea32-282">Jeśli pojawi się następujący komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="3ea32-283">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="3ea32-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="3ea32-284">Przetestuj **Create** link.</span><span class="sxs-lookup"><span data-stu-id="3ea32-284">Test the **Create** link.</span></span>

   ![::: No-Loc (Create)::: Page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="3ea32-286">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3ea32-287">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="3ea32-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3ea32-288">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="3ea32-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="3ea32-289">Przetestuj **Edytowanie** , **szczegóły** i **Delete** linki.</span><span class="sxs-lookup"><span data-stu-id="3ea32-289">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3ea32-290">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ea32-291">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3ea32-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3ea32-292">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3ea32-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="3ea32-293">W tej sekcji dodano klasy do zarządzania filmami.</span><span class="sxs-lookup"><span data-stu-id="3ea32-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="3ea32-294">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="3ea32-295">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="3ea32-296">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="3ea32-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3ea32-297">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3ea32-298">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3ea32-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3ea32-299">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-300">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-301">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3ea32-302">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-302">Name the folder *Models*.</span></span>

<span data-ttu-id="3ea32-303">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="3ea32-304">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="3ea32-305">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="3ea32-305">Name the class **Movie**.</span></span>

<span data-ttu-id="3ea32-306">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-307">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-308">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-309">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-310">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-310">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-311">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-312">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ea32-313">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3ea32-315">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3ea32-316">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3ea32-317">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-318">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-319">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-320">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-321">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-321">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-322">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-323">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ea32-324">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3ea32-325">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="3ea32-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3ea32-326">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-326">Add a database context class</span></span>

* <span data-ttu-id="3ea32-327">W projekcie *Razor PagesMovie* Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="3ea32-328">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ea32-329">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="3ea32-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3ea32-330">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="3ea32-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3ea32-331">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3ea32-332">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-332">Add a database connection string</span></span>

<span data-ttu-id="3ea32-333">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3ea32-334">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-334">Register the database context</span></span>

<span data-ttu-id="3ea32-335">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3ea32-336">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-337">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3ea32-338">W **oknie narzędzia rozwiązania** kliknij projekt **Razor PagesMovie** , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-338">In the **Solution Tool Window** , control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="3ea32-339">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="3ea32-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="3ea32-340">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3ea32-341">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="3ea32-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3ea32-342">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3ea32-343">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="3ea32-344">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-345">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-346">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-347">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-348">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-348">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-349">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-350">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="3ea32-351">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="3ea32-352">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3ea32-353">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="3ea32-353">Scaffold the movie model</span></span>

<span data-ttu-id="3ea32-354">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="3ea32-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3ea32-355">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla Create operacji, odczytu, aktualizacji i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-356">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-357">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ea32-358">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ea32-359">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ea32-360">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="3ea32-362">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="3ea32-364">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3ea32-365">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3ea32-366">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę z Razor PagesMovie. **Modele**. Razor PagesMovieContext do Razor PagesMovie. **Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="3ea32-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3ea32-367">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="3ea32-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3ea32-368">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="3ea32-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3ea32-369">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-369">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="3ea32-371">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3ea32-373">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs* , *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="3ea32-374">**Dla systemu Windows** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3ea32-375">**W przypadku systemów macOS i Linux** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3ea32-376">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="3ea32-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="3ea32-377">Opcja</span><span class="sxs-lookup"><span data-stu-id="3ea32-377">Option</span></span>               | <span data-ttu-id="3ea32-378">Opis</span><span class="sxs-lookup"><span data-stu-id="3ea32-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3ea32-379">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3ea32-380">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="3ea32-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3ea32-381">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3ea32-382">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="3ea32-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3ea32-383">Dodaje `_ValidationScriptsPartial` do edycji i Create stron</span><span class="sxs-lookup"><span data-stu-id="3ea32-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3ea32-384">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="3ea32-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3ea32-385">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="3ea32-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ea32-386">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="3ea32-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ea32-387">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="3ea32-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ea32-388">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="3ea32-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3ea32-389">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3ea32-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-390">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ea32-391">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ea32-392">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ea32-393">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ea32-394">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="3ea32-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="3ea32-396">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="3ea32-398">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3ea32-399">W menu rozwijanym **Klasa modelu** wybierz lub wpisz **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3ea32-400">W wierszu **klasy kontekstu danych** wpisz nazwę nowej klasy, Razor PagesMovie. **Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="3ea32-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3ea32-401">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="3ea32-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3ea32-402">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="3ea32-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3ea32-403">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-403">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="3ea32-405">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="3ea32-406">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="3ea32-406">Add EF tools</span></span>

<span data-ttu-id="3ea32-407">Uruchom następujące interfejs wiersza polecenia platformy .NET Core polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="3ea32-408">Poprzednie polecenie dodaje Entity Framework Core narzędzia dla interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ea32-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="3ea32-409">Aby uzyskać więcej informacji, zobacz [Entity Framework Core Tools Reference-interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="3ea32-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ea32-410">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="3ea32-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ea32-411">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="3ea32-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ea32-412">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="3ea32-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3ea32-413">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3ea32-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="3ea32-414">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="3ea32-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-415">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-416">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ea32-417">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-417">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ea32-418">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ea32-419">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-419">Updated</span></span>

* <span data-ttu-id="3ea32-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-420">*Startup.cs*</span></span>

<span data-ttu-id="3ea32-421">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-422">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ea32-423">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ea32-424">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-424">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ea32-425">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ea32-426">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-426">Updated</span></span>

* <span data-ttu-id="3ea32-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-427">*Startup.cs*</span></span>

<span data-ttu-id="3ea32-428">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3ea32-430">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="3ea32-431">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-431">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="3ea32-432">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3ea32-433">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="3ea32-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-434">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-435">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="3ea32-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3ea32-436">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-436">Add an initial migration.</span></span>
* <span data-ttu-id="3ea32-437">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="3ea32-438">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3ea32-440">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3ea32-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-441">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3ea32-442">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="3ea32-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="3ea32-443">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="3ea32-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3ea32-444">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="3ea32-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3ea32-445">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="3ea32-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3ea32-446">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="3ea32-447">Polecenie migrations generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="3ea32-448">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="3ea32-449">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3ea32-450">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="3ea32-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3ea32-451">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="3ea32-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="3ea32-452">W takim przypadku program `update` uruchamia `Up` metodę w pliku  *migrations/ \<time-stamp> _Initial Create . cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-453">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3ea32-454">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="3ea32-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3ea32-455">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3ea32-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3ea32-456">Usługi, takie jak kontekst kontekstu bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3ea32-457">Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="3ea32-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="3ea32-458">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="3ea32-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3ea32-459">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="3ea32-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3ea32-460">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="3ea32-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3ea32-461">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="3ea32-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3ea32-462">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak Create , Odczyt, aktualizacja i Delete , dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3ea32-463">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="3ea32-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3ea32-464">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ea32-465">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="3ea32-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3ea32-466">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3ea32-467">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="3ea32-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3ea32-468">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="3ea32-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3ea32-469">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-470">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3ea32-471">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="3ea32-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3ea32-472">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-472">Test the app</span></span>

* <span data-ttu-id="3ea32-473">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3ea32-474">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="3ea32-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3ea32-475">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="3ea32-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3ea32-476">Przetestuj **Create** link.</span><span class="sxs-lookup"><span data-stu-id="3ea32-476">Test the **Create** link.</span></span>

  ![::: No-Loc (Create)::: Page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="3ea32-478">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3ea32-479">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="3ea32-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3ea32-480">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="3ea32-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3ea32-481">Przetestuj **Edytowanie** , **szczegóły** i **Delete** linki.</span><span class="sxs-lookup"><span data-stu-id="3ea32-481">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3ea32-482">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ea32-483">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3ea32-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3ea32-484">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3ea32-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3ea32-485">W tej sekcji są dodawane klasy do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="3ea32-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="3ea32-486">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="3ea32-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="3ea32-487">Klasy modelu aplikacji są używane z [Entity Framework Core (Ef Core)](/ef/core) ([dostawca bazy danych programu SQLite EF Core](/ef/core/providers/sqlite)) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="3ea32-488">EF Core to struktura obiektów mapowania relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="3ea32-489">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="3ea32-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3ea32-490">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3ea32-491">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3ea32-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3ea32-492">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-493">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-494">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3ea32-495">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-495">Name the folder *Models*.</span></span>

<span data-ttu-id="3ea32-496">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="3ea32-497">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="3ea32-498">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="3ea32-498">Name the class **Movie**.</span></span>

<span data-ttu-id="3ea32-499">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-500">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-501">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-502">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-503">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-503">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-504">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-505">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ea32-506">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3ea32-508">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3ea32-509">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3ea32-510">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-511">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-512">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-513">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-514">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-514">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-515">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-516">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ea32-517">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3ea32-518">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="3ea32-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3ea32-519">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-519">Add a database context class</span></span>

<span data-ttu-id="3ea32-520">W Razor projekcie PagesMovie Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="3ea32-521">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ea32-522">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="3ea32-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3ea32-523">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="3ea32-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3ea32-524">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3ea32-525">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-525">Add a database connection string</span></span>

<span data-ttu-id="3ea32-526">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="3ea32-527">Dodaj wymagane pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="3ea32-527">Add required NuGet packages</span></span>

<span data-ttu-id="3ea32-528">Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="3ea32-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="3ea32-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3ea32-530">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3ea32-530">Register the database context</span></span>

<span data-ttu-id="3ea32-531">Dodaj następujące `using` instrukcje w górnej części *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3ea32-532">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="3ea32-533">Kompiluj projekt jako sprawdzenie pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-534">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3ea32-535">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-535">In **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="3ea32-536">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="3ea32-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="3ea32-537">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="3ea32-538">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3ea32-539">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="3ea32-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3ea32-540">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3ea32-541">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="3ea32-542">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="3ea32-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ea32-543">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="3ea32-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ea32-544">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ea32-545">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ea32-546">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="3ea32-546">With this attribute:</span></span>

  * <span data-ttu-id="3ea32-547">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="3ea32-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ea32-548">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="3ea32-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ea32-549">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="3ea32-550">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3ea32-551">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="3ea32-551">Scaffold the movie model</span></span>

<span data-ttu-id="3ea32-552">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="3ea32-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3ea32-553">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla Create operacji, odczytu, aktualizacji i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-554">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-555">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ea32-556">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ea32-557">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ea32-558">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="3ea32-560">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="3ea32-562">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="3ea32-563">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3ea32-564">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zaakceptuj wygenerowaną nazwę **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3ea32-565">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-565">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="3ea32-567">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ea32-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ea32-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3ea32-569">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs* , *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="3ea32-570">**Dla systemu Windows** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3ea32-571">**W przypadku systemów macOS i Linux** : Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3ea32-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3ea32-572">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="3ea32-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="3ea32-573">Opcja</span><span class="sxs-lookup"><span data-stu-id="3ea32-573">Option</span></span>               | <span data-ttu-id="3ea32-574">Opis</span><span class="sxs-lookup"><span data-stu-id="3ea32-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3ea32-575">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3ea32-576">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="3ea32-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3ea32-577">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="3ea32-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3ea32-578">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="3ea32-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3ea32-579">Dodaje `_ValidationScriptsPartial` do edycji i Create stron</span><span class="sxs-lookup"><span data-stu-id="3ea32-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3ea32-580">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="3ea32-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3ea32-581">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="3ea32-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-582">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ea32-583">Create folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="3ea32-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ea32-584">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ea32-585">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="3ea32-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ea32-586">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="3ea32-588">W oknie dialogowym **Dodawanie nowej szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="3ea32-590">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3ea32-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3ea32-591">Z listy rozwijanej **Klasa modelu** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="3ea32-592">W wierszu **klasy kontekstu danych** wpisz wybierz **Razor PagesMovieContext** to spowoduje utworzenie nowej klasy kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="3ea32-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="3ea32-593">W takim przypadku będzie to **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3ea32-594">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-594">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="3ea32-596">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="3ea32-597">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="3ea32-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3ea32-598">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="3ea32-598">Files created</span></span>

* <span data-ttu-id="3ea32-599">*Strony/filmy* : Create , Delete , szczegóły, Edytuj i Index .</span><span class="sxs-lookup"><span data-stu-id="3ea32-599">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ea32-600">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="3ea32-601">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="3ea32-601">File updated</span></span>

* <span data-ttu-id="3ea32-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ea32-602">*Startup.cs*</span></span>

<span data-ttu-id="3ea32-603">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3ea32-604">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="3ea32-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-605">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ea32-606">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="3ea32-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3ea32-607">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-607">Add an initial migration.</span></span>
* <span data-ttu-id="3ea32-608">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="3ea32-609">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="3ea32-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3ea32-611">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3ea32-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="3ea32-612">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3ea32-613">Schemat jest oparty na modelu określonym w `DbContext` , w pliku *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="3ea32-614">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="3ea32-615">Można użyć dowolnej nazwy, ale według Konwencji Nazwa opisująca migrację jest używana.</span><span class="sxs-lookup"><span data-stu-id="3ea32-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="3ea32-616">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="3ea32-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="3ea32-617">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _Initial Create . cs* .</span><span class="sxs-lookup"><span data-stu-id="3ea32-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3ea32-618">`Up`Metoda tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-619">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3ea32-620">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="3ea32-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="3ea32-621">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="3ea32-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3ea32-622">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="3ea32-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3ea32-623">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="3ea32-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3ea32-624">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ea32-625">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ea32-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3ea32-626">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="3ea32-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3ea32-627">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3ea32-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3ea32-628">Usługi (takie jak kontekst kontekstu bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3ea32-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3ea32-629">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="3ea32-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3ea32-630">Kod konstruktora, który pobiera wystąpienie kontekstu contextB kontekstu bazy danych jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="3ea32-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3ea32-631">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="3ea32-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3ea32-632">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="3ea32-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3ea32-633">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="3ea32-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="3ea32-634">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak Create , Odczyt, aktualizacja i Delete , dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3ea32-635">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="3ea32-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3ea32-636">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ea32-637">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="3ea32-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3ea32-638">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3ea32-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3ea32-639">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="3ea32-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3ea32-640">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="3ea32-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3ea32-641">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="3ea32-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ea32-642">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3ea32-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3ea32-643">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="3ea32-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3ea32-644">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="3ea32-644">Test the app</span></span>

* <span data-ttu-id="3ea32-645">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="3ea32-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3ea32-646">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="3ea32-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3ea32-647">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="3ea32-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3ea32-648">Przetestuj **Create** link.</span><span class="sxs-lookup"><span data-stu-id="3ea32-648">Test the **Create** link.</span></span>

  ![::: No-Loc (Create)::: Page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="3ea32-650">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="3ea32-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3ea32-651">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="3ea32-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3ea32-652">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="3ea32-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3ea32-653">Przetestuj **Edytowanie** , **szczegóły** i **Delete** linki.</span><span class="sxs-lookup"><span data-stu-id="3ea32-653">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3ea32-654">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="3ea32-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ea32-655">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3ea32-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3ea32-656">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3ea32-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
