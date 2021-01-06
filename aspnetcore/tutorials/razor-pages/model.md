---
title: Część 2, Dodawanie modelu
author: rick-anderson
description: Część 2 serii samouczków na Razor stronach. W tej sekcji dodawane są klasy modelu.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485995"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="549ec-104">Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="549ec-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="549ec-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="549ec-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="549ec-106">W tej sekcji są dodawane klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="549ec-107">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="549ec-108">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="549ec-109">Najpierw należy napisać klasy modelu, a EF Core utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="549ec-110">Klasy modelu są znane jako klasy POCO (od "**P** Lain-**O** LD **k** LR **O** biekty"), ponieważ nie mają zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="549ec-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="549ec-111">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="549ec-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="549ec-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="549ec-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="549ec-113">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="549ec-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="549ec-115">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt *Razor PagesMovie* > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="549ec-116">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="549ec-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="549ec-117">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="549ec-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="549ec-118">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="549ec-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="549ec-119">Nazwij *film* klasy.</span><span class="sxs-lookup"><span data-stu-id="549ec-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="549ec-120">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-121">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-122">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-123">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-124">With this attribute:</span></span>

  * <span data-ttu-id="549ec-125">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-126">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="549ec-128">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="549ec-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="549ec-129">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="549ec-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="549ec-130">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-131">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-132">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-133">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-134">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-134">With this attribute:</span></span>

  * <span data-ttu-id="549ec-135">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-136">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="549ec-137">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="549ec-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="549ec-138">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="549ec-138">Add a database context class</span></span>

1. <span data-ttu-id="549ec-139">W projekcie *Razor PagesMovie* Utwórz folder o nazwie *dane*.</span><span class="sxs-lookup"><span data-stu-id="549ec-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="549ec-140">W folderze *dane* Dodaj plik o nazwie *Razor PagesMovieContext.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="549ec-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="549ec-141">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="549ec-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="549ec-142">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="549ec-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="549ec-143">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="549ec-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="549ec-144">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="549ec-144">Add a database connection string</span></span>

<span data-ttu-id="549ec-145">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="549ec-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="549ec-146">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="549ec-146">Register the database context</span></span>

1. <span data-ttu-id="549ec-147">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="549ec-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="549ec-148">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="549ec-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-149">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="549ec-150">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="549ec-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="549ec-151">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="549ec-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="549ec-152">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="549ec-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="549ec-153">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="549ec-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="549ec-154">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="549ec-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="549ec-155">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="549ec-156">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-157">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-158">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-159">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-160">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-160">With this attribute:</span></span>

  * <span data-ttu-id="549ec-161">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-162">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="549ec-163">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="549ec-164">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="549ec-165">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="549ec-165">Scaffold the movie model</span></span>

<span data-ttu-id="549ec-166">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="549ec-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="549ec-167">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="549ec-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-168">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="549ec-169">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="549ec-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="549ec-170">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="549ec-171">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="549ec-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="549ec-172">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/sca.png)

1. <span data-ttu-id="549ec-174">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

1. <span data-ttu-id="549ec-176">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="549ec-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="549ec-177">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="549ec-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="549ec-178">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="549ec-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="549ec-179">W oknie dialogowym **Dodawanie kontekstu danych** nazwa klasy *Razor PagesMovie. Data. Razor PagesMovieContext* jest generowany.</span><span class="sxs-lookup"><span data-stu-id="549ec-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="549ec-180">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-180">Select **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="549ec-182">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="549ec-184">Otwórz powłokę poleceń w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="549ec-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="549ec-185">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="549ec-186">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="549ec-187">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcji generatora kodu.</span><span class="sxs-lookup"><span data-stu-id="549ec-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="549ec-188">Opcja</span><span class="sxs-lookup"><span data-stu-id="549ec-188">Option</span></span>               | <span data-ttu-id="549ec-189">Opis</span><span class="sxs-lookup"><span data-stu-id="549ec-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="549ec-190">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="549ec-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="549ec-191">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="549ec-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="549ec-192">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="549ec-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="549ec-193">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="549ec-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="549ec-194">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron</span><span class="sxs-lookup"><span data-stu-id="549ec-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="549ec-195">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="549ec-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="549ec-196">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="549ec-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="549ec-197">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="549ec-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="549ec-198">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="549ec-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="549ec-199">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="549ec-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="549ec-200">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="549ec-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-201">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="549ec-202">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="549ec-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="549ec-203">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="549ec-204">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="549ec-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="549ec-205">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="549ec-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

1. <span data-ttu-id="549ec-207">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="549ec-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="549ec-209">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="549ec-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="549ec-210">W **klasie DbContext, aby użyć:** Row, nazwij klasę *Razor PagesMovie. Data. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="549ec-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="549ec-211">Wybierz pozycję **Zakończ**.</span><span class="sxs-lookup"><span data-stu-id="549ec-211">Select **Finish**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/arpMac.png)

<span data-ttu-id="549ec-213">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="549ec-214">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="549ec-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="549ec-215">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="549ec-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="549ec-216">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="549ec-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="549ec-217">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="549ec-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="549ec-218">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="549ec-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-220">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="549ec-221">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="549ec-222">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="549ec-223">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="549ec-223">Updated</span></span>

* <span data-ttu-id="549ec-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-224">*Startup.cs*</span></span>

<span data-ttu-id="549ec-225">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="549ec-227">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="549ec-228">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="549ec-229">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-230">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="549ec-231">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="549ec-232">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="549ec-233">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="549ec-234">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="549ec-234">Updated</span></span>

* <span data-ttu-id="549ec-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-235">*Startup.cs*</span></span>

<span data-ttu-id="549ec-236">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="549ec-237">Utwórz początkowy schemat bazy danych przy użyciu funkcji migracji EF</span><span class="sxs-lookup"><span data-stu-id="549ec-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="549ec-238">Funkcja migracji w Entity Framework Core zapewnia sposób:</span><span class="sxs-lookup"><span data-stu-id="549ec-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="549ec-239">Utwórz początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-239">Create the initial database schema.</span></span>
* <span data-ttu-id="549ec-240">Uaktualnij przyrostowo schemat bazy danych, aby zachować synchronizację z modelem danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="549ec-241">Istniejące dane w bazie danych są zachowywane.</span><span class="sxs-lookup"><span data-stu-id="549ec-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-242">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-243">W tej sekcji okno **konsoli Menedżera pakietów** (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="549ec-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="549ec-244">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-244">Add an initial migration.</span></span>
* <span data-ttu-id="549ec-245">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="549ec-246">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="549ec-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="549ec-248">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="549ec-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="549ec-249">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="549ec-250">Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="549ec-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="549ec-251">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="549ec-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="549ec-252">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="549ec-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="549ec-253">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="549ec-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="549ec-254">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="549ec-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="549ec-255">`migrations`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="549ec-256">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="549ec-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="549ec-257">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="549ec-258">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="549ec-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="549ec-259">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="549ec-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="549ec-260">W takim przypadku program `update` uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-261">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="549ec-262">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="549ec-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="549ec-263">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="549ec-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="549ec-264">Usługi, takie jak kontekst bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="549ec-265">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="549ec-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="549ec-266">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="549ec-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="549ec-267">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="549ec-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="549ec-268">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="549ec-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="549ec-269">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="549ec-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="549ec-270">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="549ec-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="549ec-271">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="549ec-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="549ec-272">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="549ec-273">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="549ec-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="549ec-274">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="549ec-275">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="549ec-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="549ec-276">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="549ec-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="549ec-277">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="549ec-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="549ec-278">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="549ec-279">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="549ec-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="549ec-280">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="549ec-280">Test the app</span></span>

1. <span data-ttu-id="549ec-281">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="549ec-282">Jeśli pojawi się następujący komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="549ec-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="549ec-283">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="549ec-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="549ec-284">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="549ec-284">Test the **Create** link.</span></span>

   ![Tworzenie strony](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="549ec-286">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="549ec-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="549ec-287">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="549ec-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="549ec-288">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="549ec-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="549ec-289">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="549ec-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="549ec-290">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="549ec-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="549ec-291">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="549ec-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="549ec-292">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="549ec-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="549ec-293">W tej sekcji dodano klasy do zarządzania filmami.</span><span class="sxs-lookup"><span data-stu-id="549ec-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="549ec-294">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="549ec-295">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="549ec-296">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="549ec-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="549ec-297">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="549ec-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="549ec-298">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="549ec-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="549ec-299">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="549ec-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-300">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-301">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="549ec-302">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="549ec-302">Name the folder *Models*.</span></span>

<span data-ttu-id="549ec-303">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="549ec-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="549ec-304">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="549ec-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="549ec-305">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="549ec-305">Name the class **Movie**.</span></span>

<span data-ttu-id="549ec-306">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-307">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-308">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-309">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-310">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-310">With this attribute:</span></span>

  * <span data-ttu-id="549ec-311">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-312">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="549ec-313">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="549ec-315">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="549ec-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="549ec-316">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="549ec-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="549ec-317">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-318">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-319">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-320">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-321">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-321">With this attribute:</span></span>

  * <span data-ttu-id="549ec-322">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-323">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="549ec-324">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="549ec-325">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="549ec-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="549ec-326">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="549ec-326">Add a database context class</span></span>

* <span data-ttu-id="549ec-327">W projekcie *Razor PagesMovie* Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="549ec-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="549ec-328">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="549ec-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="549ec-329">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="549ec-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="549ec-330">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="549ec-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="549ec-331">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="549ec-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="549ec-332">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="549ec-332">Add a database connection string</span></span>

<span data-ttu-id="549ec-333">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="549ec-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="549ec-334">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="549ec-334">Register the database context</span></span>

<span data-ttu-id="549ec-335">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="549ec-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="549ec-336">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="549ec-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-337">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="549ec-338">W **oknie narzędzia rozwiązania** kliknij projekt **Razor PagesMovie** , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="549ec-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="549ec-339">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="549ec-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="549ec-340">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="549ec-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="549ec-341">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="549ec-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="549ec-342">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="549ec-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="549ec-343">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="549ec-344">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-345">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-346">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-347">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-348">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-348">With this attribute:</span></span>

  * <span data-ttu-id="549ec-349">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-350">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="549ec-351">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="549ec-352">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="549ec-353">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="549ec-353">Scaffold the movie model</span></span>

<span data-ttu-id="549ec-354">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="549ec-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="549ec-355">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="549ec-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-356">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-357">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="549ec-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="549ec-358">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="549ec-359">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="549ec-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="549ec-360">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="549ec-362">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="549ec-364">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="549ec-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="549ec-365">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="549ec-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="549ec-366">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę z Razor PagesMovie.**Modele**. Razor PagesMovieContext do Razor PagesMovie.**Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="549ec-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="549ec-367">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="549ec-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="549ec-368">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="549ec-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="549ec-369">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-369">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="549ec-371">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="549ec-373">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="549ec-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="549ec-374">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="549ec-375">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="549ec-376">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="549ec-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="549ec-377">Opcja</span><span class="sxs-lookup"><span data-stu-id="549ec-377">Option</span></span>               | <span data-ttu-id="549ec-378">Opis</span><span class="sxs-lookup"><span data-stu-id="549ec-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="549ec-379">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="549ec-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="549ec-380">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="549ec-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="549ec-381">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="549ec-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="549ec-382">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="549ec-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="549ec-383">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron</span><span class="sxs-lookup"><span data-stu-id="549ec-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="549ec-384">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="549ec-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="549ec-385">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="549ec-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="549ec-386">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="549ec-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="549ec-387">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="549ec-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="549ec-388">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="549ec-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="549ec-389">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="549ec-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-390">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="549ec-391">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="549ec-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="549ec-392">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="549ec-393">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="549ec-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="549ec-394">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="549ec-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="549ec-396">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="549ec-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="549ec-398">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="549ec-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="549ec-399">W menu rozwijanym **Klasa modelu** wybierz lub wpisz **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="549ec-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="549ec-400">W wierszu **klasy kontekstu danych** wpisz nazwę nowej klasy, Razor PagesMovie.**Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="549ec-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="549ec-401">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="549ec-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="549ec-402">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="549ec-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="549ec-403">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-403">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="549ec-405">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="549ec-406">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="549ec-406">Add EF tools</span></span>

<span data-ttu-id="549ec-407">Uruchom następujące interfejs wiersza polecenia platformy .NET Core polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="549ec-408">Poprzednie polecenie dodaje Entity Framework Core narzędzia dla interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="549ec-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="549ec-409">Aby uzyskać więcej informacji, zobacz [Entity Framework Core Tools Reference-interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="549ec-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="549ec-410">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="549ec-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="549ec-411">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="549ec-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="549ec-412">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="549ec-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="549ec-413">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="549ec-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="549ec-414">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="549ec-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-415">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-416">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="549ec-417">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="549ec-418">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="549ec-419">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="549ec-419">Updated</span></span>

* <span data-ttu-id="549ec-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-420">*Startup.cs*</span></span>

<span data-ttu-id="549ec-421">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-422">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="549ec-423">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="549ec-424">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="549ec-425">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="549ec-426">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="549ec-426">Updated</span></span>

* <span data-ttu-id="549ec-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-427">*Startup.cs*</span></span>

<span data-ttu-id="549ec-428">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="549ec-430">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="549ec-431">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="549ec-432">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="549ec-433">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="549ec-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-434">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-435">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="549ec-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="549ec-436">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-436">Add an initial migration.</span></span>
* <span data-ttu-id="549ec-437">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="549ec-438">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="549ec-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="549ec-440">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="549ec-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="549ec-441">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="549ec-442">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="549ec-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="549ec-443">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="549ec-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="549ec-444">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="549ec-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="549ec-445">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="549ec-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="549ec-446">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="549ec-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="549ec-447">Polecenie migrations generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="549ec-448">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="549ec-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="549ec-449">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="549ec-450">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="549ec-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="549ec-451">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="549ec-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="549ec-452">W takim przypadku program `update` uruchamia `Up` metodę w pliku  *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-453">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="549ec-454">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="549ec-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="549ec-455">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="549ec-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="549ec-456">Usługi, takie jak kontekst kontekstu bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="549ec-457">Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="549ec-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="549ec-458">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="549ec-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="549ec-459">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="549ec-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="549ec-460">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="549ec-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="549ec-461">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="549ec-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="549ec-462">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="549ec-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="549ec-463">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="549ec-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="549ec-464">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="549ec-465">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="549ec-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="549ec-466">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="549ec-467">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="549ec-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="549ec-468">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="549ec-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="549ec-469">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="549ec-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="549ec-470">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="549ec-471">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="549ec-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="549ec-472">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="549ec-472">Test the app</span></span>

* <span data-ttu-id="549ec-473">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="549ec-474">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="549ec-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="549ec-475">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="549ec-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="549ec-476">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="549ec-476">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="549ec-478">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="549ec-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="549ec-479">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="549ec-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="549ec-480">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="549ec-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="549ec-481">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="549ec-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="549ec-482">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="549ec-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="549ec-483">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="549ec-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="549ec-484">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="549ec-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="549ec-485">W tej sekcji są dodawane klasy do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="549ec-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="549ec-486">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="549ec-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="549ec-487">Klasy modelu aplikacji są używane z [Entity Framework Core (Ef Core)](/ef/core) ([dostawca bazy danych programu SQLite EF Core](/ef/core/providers/sqlite)) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="549ec-488">EF Core to struktura obiektów mapowania relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="549ec-489">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="549ec-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="549ec-490">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="549ec-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="549ec-491">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="549ec-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="549ec-492">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="549ec-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-493">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-494">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="549ec-495">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="549ec-495">Name the folder *Models*.</span></span>

<span data-ttu-id="549ec-496">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="549ec-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="549ec-497">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="549ec-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="549ec-498">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="549ec-498">Name the class **Movie**.</span></span>

<span data-ttu-id="549ec-499">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-500">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-501">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-502">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-503">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-503">With this attribute:</span></span>

  * <span data-ttu-id="549ec-504">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-505">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="549ec-506">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="549ec-508">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="549ec-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="549ec-509">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="549ec-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="549ec-510">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-511">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-512">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-513">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-514">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-514">With this attribute:</span></span>

  * <span data-ttu-id="549ec-515">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-516">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="549ec-517">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="549ec-518">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="549ec-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="549ec-519">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="549ec-519">Add a database context class</span></span>

<span data-ttu-id="549ec-520">W Razor projekcie PagesMovie Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="549ec-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="549ec-521">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="549ec-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="549ec-522">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="549ec-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="549ec-523">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="549ec-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="549ec-524">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="549ec-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="549ec-525">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="549ec-525">Add a database connection string</span></span>

<span data-ttu-id="549ec-526">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="549ec-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="549ec-527">Dodaj wymagane pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="549ec-527">Add required NuGet packages</span></span>

<span data-ttu-id="549ec-528">Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="549ec-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="549ec-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="549ec-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="549ec-530">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="549ec-530">Register the database context</span></span>

<span data-ttu-id="549ec-531">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="549ec-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="549ec-532">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="549ec-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="549ec-533">Kompiluj projekt jako sprawdzenie pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="549ec-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-534">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="549ec-535">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="549ec-536">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="549ec-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="549ec-537">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="549ec-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="549ec-538">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="549ec-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="549ec-539">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="549ec-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="549ec-540">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="549ec-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="549ec-541">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="549ec-542">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="549ec-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="549ec-543">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="549ec-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="549ec-544">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="549ec-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="549ec-545">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="549ec-546">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="549ec-546">With this attribute:</span></span>

  * <span data-ttu-id="549ec-547">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="549ec-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="549ec-548">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="549ec-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="549ec-549">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="549ec-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="549ec-550">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="549ec-551">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="549ec-551">Scaffold the movie model</span></span>

<span data-ttu-id="549ec-552">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="549ec-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="549ec-553">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="549ec-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-554">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-555">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="549ec-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="549ec-556">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="549ec-557">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="549ec-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="549ec-558">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="549ec-560">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="549ec-562">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="549ec-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="549ec-563">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="549ec-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="549ec-564">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zaakceptuj wygenerowaną nazwę **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="549ec-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="549ec-565">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-565">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="549ec-567">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="549ec-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="549ec-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="549ec-569">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="549ec-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="549ec-570">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="549ec-571">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="549ec-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="549ec-572">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="549ec-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="549ec-573">Opcja</span><span class="sxs-lookup"><span data-stu-id="549ec-573">Option</span></span>               | <span data-ttu-id="549ec-574">Opis</span><span class="sxs-lookup"><span data-stu-id="549ec-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="549ec-575">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="549ec-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="549ec-576">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="549ec-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="549ec-577">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="549ec-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="549ec-578">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="549ec-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="549ec-579">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron</span><span class="sxs-lookup"><span data-stu-id="549ec-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="549ec-580">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="549ec-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="549ec-581">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="549ec-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="549ec-582">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="549ec-583">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="549ec-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="549ec-584">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="549ec-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="549ec-585">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="549ec-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="549ec-586">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="549ec-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="549ec-588">W oknie dialogowym **Dodawanie nowej szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="549ec-590">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="549ec-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="549ec-591">Z listy rozwijanej **Klasa modelu** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="549ec-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="549ec-592">W wierszu **klasy kontekstu danych** wpisz wybierz **Razor PagesMovieContext** to spowoduje utworzenie nowej klasy kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="549ec-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="549ec-593">W takim przypadku będzie to **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="549ec-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="549ec-594">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="549ec-594">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="549ec-596">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="549ec-597">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="549ec-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="549ec-598">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="549ec-598">Files created</span></span>

* <span data-ttu-id="549ec-599">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="549ec-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="549ec-600">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="549ec-601">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="549ec-601">File updated</span></span>

* <span data-ttu-id="549ec-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="549ec-602">*Startup.cs*</span></span>

<span data-ttu-id="549ec-603">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="549ec-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="549ec-604">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="549ec-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-605">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="549ec-606">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="549ec-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="549ec-607">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-607">Add an initial migration.</span></span>
* <span data-ttu-id="549ec-608">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="549ec-609">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="549ec-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="549ec-611">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="549ec-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="549ec-612">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="549ec-613">Schemat jest oparty na modelu określonym w `DbContext` , w pliku *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="549ec-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="549ec-614">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="549ec-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="549ec-615">Można użyć dowolnej nazwy, ale według Konwencji Nazwa opisująca migrację jest używana.</span><span class="sxs-lookup"><span data-stu-id="549ec-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="549ec-616">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="549ec-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="549ec-617">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="549ec-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="549ec-618">`Up`Metoda tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="549ec-619">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="549ec-620">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="549ec-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="549ec-621">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="549ec-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="549ec-622">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="549ec-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="549ec-623">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="549ec-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="549ec-624">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="549ec-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="549ec-625">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="549ec-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="549ec-626">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="549ec-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="549ec-627">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="549ec-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="549ec-628">Usługi (takie jak kontekst kontekstu bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="549ec-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="549ec-629">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="549ec-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="549ec-630">Kod konstruktora, który pobiera wystąpienie kontekstu contextB kontekstu bazy danych jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="549ec-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="549ec-631">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="549ec-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="549ec-632">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="549ec-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="549ec-633">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="549ec-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="549ec-634">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="549ec-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="549ec-635">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="549ec-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="549ec-636">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="549ec-637">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="549ec-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="549ec-638">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="549ec-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="549ec-639">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="549ec-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="549ec-640">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="549ec-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="549ec-641">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="549ec-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="549ec-642">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="549ec-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="549ec-643">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="549ec-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="549ec-644">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="549ec-644">Test the app</span></span>

* <span data-ttu-id="549ec-645">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="549ec-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="549ec-646">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="549ec-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="549ec-647">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="549ec-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="549ec-648">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="549ec-648">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="549ec-650">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="549ec-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="549ec-651">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="549ec-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="549ec-652">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="549ec-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="549ec-653">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="549ec-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="549ec-654">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="549ec-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="549ec-655">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="549ec-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="549ec-656">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="549ec-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
