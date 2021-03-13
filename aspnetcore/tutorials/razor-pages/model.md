---
title: Część 2, Dodawanie modelu
author: rick-anderson
description: Część 2 serii samouczków na Razor stronach. W tej sekcji dodawane są klasy modelu.
ms.author: riande
ms.date: 03/10/2021
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
ms.openlocfilehash: defbc73d0c1d6aac30360cd7b83cc518a407bf98
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413447"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e509c-104">Część 2 Dodaj model do Razor aplikacji stron w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e509c-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e509c-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e509c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e509c-106">W tej sekcji są dodawane klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="e509c-107">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e509c-108">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="e509c-109">Najpierw należy napisać klasy modelu, a EF Core utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="e509c-110">Klasy modelu są znane jako klasy POCO (od "**P** Lain-**O** LD **k** LR **O** biekty"), ponieważ nie mają zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="e509c-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="e509c-111">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="e509c-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e509c-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e509c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e509c-113">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="e509c-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e509c-115">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt *Razor PagesMovie* > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e509c-116">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="e509c-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="e509c-117">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="e509c-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="e509c-118">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="e509c-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="e509c-119">Nazwij *film* klasy.</span><span class="sxs-lookup"><span data-stu-id="e509c-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="e509c-120">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-121">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-122">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-123">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-124">With this attribute:</span></span>

  * <span data-ttu-id="e509c-125">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-126">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e509c-128">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="e509c-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="e509c-129">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e509c-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e509c-130">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-131">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-132">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-133">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-134">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-134">With this attribute:</span></span>

  * <span data-ttu-id="e509c-135">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-136">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e509c-137">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="e509c-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-138">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e509c-139">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="e509c-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="e509c-140">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="e509c-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="e509c-141">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="e509c-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="e509c-142">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="e509c-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="e509c-143">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="e509c-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="e509c-144">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="e509c-145">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-146">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-147">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-148">`[DataType(DataType.Date)]`: Atrybut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-149">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-149">With this attribute:</span></span>

  * <span data-ttu-id="e509c-150">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-151">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e509c-152">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e509c-153">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e509c-154">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="e509c-154">Scaffold the movie model</span></span>

<span data-ttu-id="e509c-155">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="e509c-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e509c-156">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="e509c-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e509c-158">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="e509c-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e509c-159">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e509c-160">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="e509c-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e509c-161">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/sca.png)

1. <span data-ttu-id="e509c-163">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

1. <span data-ttu-id="e509c-165">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e509c-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e509c-166">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="e509c-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="e509c-167">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="e509c-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="e509c-168">W oknie dialogowym **Dodawanie kontekstu danych** `RazorPagesMovie.Data.RazorPagesMovieContext` jest generowana nazwa klasy.</span><span class="sxs-lookup"><span data-stu-id="e509c-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="e509c-169">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-169">Select **Add**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="e509c-171">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e509c-173">Otwórz powłokę poleceń w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e509c-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e509c-174">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e509c-175">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e509c-176">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcji generatora kodu.</span><span class="sxs-lookup"><span data-stu-id="e509c-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="e509c-177">Opcja</span><span class="sxs-lookup"><span data-stu-id="e509c-177">Option</span></span>               | <span data-ttu-id="e509c-178">Opis</span><span class="sxs-lookup"><span data-stu-id="e509c-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e509c-179">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="e509c-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e509c-180">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="e509c-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e509c-181">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="e509c-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e509c-182">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="e509c-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e509c-183">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron</span><span class="sxs-lookup"><span data-stu-id="e509c-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e509c-184">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="e509c-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e509c-185">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e509c-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e509c-186">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="e509c-186">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e509c-187">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="e509c-187">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e509c-188">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e509c-188">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e509c-189">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="e509c-189">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-190">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e509c-191">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="e509c-191">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e509c-192">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-192">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e509c-193">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="e509c-193">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e509c-194">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="e509c-194">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

1. <span data-ttu-id="e509c-196">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="e509c-196">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="e509c-198">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e509c-198">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e509c-199">W **klasie DbContext, aby użyć:** Row, nadaj jej nazwę Class `RazorPagesMovie.Data.RazorPagesMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="e509c-199">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="e509c-200">Wybierz pozycję **Zakończ**.</span><span class="sxs-lookup"><span data-stu-id="e509c-200">Select **Finish**.</span></span>

   ![Obraz z poprzednich instrukcji.](model/_static/5/arpMac.png)

<span data-ttu-id="e509c-202">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-202">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e509c-203">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="e509c-203">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e509c-204">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="e509c-204">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e509c-205">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e509c-205">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e509c-206">`IWebHostEnvironment` jest wstrzykiwana, aby aplikacja mogła używać oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="e509c-206">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e509c-207">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="e509c-207">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-208">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-209">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-209">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e509c-210">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-210">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e509c-211">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-211">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e509c-212">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="e509c-212">Updated</span></span>

* <span data-ttu-id="e509c-213">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-213">*Startup.cs*</span></span>

<span data-ttu-id="e509c-214">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-214">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-215">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e509c-216">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-216">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e509c-217">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-217">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e509c-218">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-218">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-219">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e509c-220">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e509c-221">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e509c-222">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e509c-223">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="e509c-223">Updated</span></span>

* <span data-ttu-id="e509c-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-224">*Startup.cs*</span></span>

<span data-ttu-id="e509c-225">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-225">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="e509c-226">Utwórz początkowy schemat bazy danych przy użyciu funkcji migracji EF</span><span class="sxs-lookup"><span data-stu-id="e509c-226">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="e509c-227">Funkcja migracji w Entity Framework Core zapewnia sposób:</span><span class="sxs-lookup"><span data-stu-id="e509c-227">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="e509c-228">Utwórz początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-228">Create the initial database schema.</span></span>
* <span data-ttu-id="e509c-229">Uaktualnij przyrostowo schemat bazy danych, aby zachować synchronizację z modelem danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-229">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="e509c-230">Istniejące dane w bazie danych są zachowywane.</span><span class="sxs-lookup"><span data-stu-id="e509c-230">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-232">W tej sekcji okno **konsoli Menedżera pakietów** (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="e509c-232">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="e509c-233">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-233">Add an initial migration.</span></span>
* <span data-ttu-id="e509c-234">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-234">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="e509c-235">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="e509c-235">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![Menu PMC](model/_static/5/pmc.png)

1. <span data-ttu-id="e509c-237">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="e509c-237">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e509c-238">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="e509c-239">Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="e509c-239">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="e509c-240">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="e509c-240">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e509c-241">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="e509c-241">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e509c-242">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="e509c-242">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e509c-243">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="e509c-243">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e509c-244">`migrations`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-244">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e509c-245">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="e509c-245">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e509c-246">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-246">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e509c-247">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="e509c-247">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e509c-248">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="e509c-248">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e509c-249">W takim przypadku program `update` uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-249">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-250">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e509c-251">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="e509c-251">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e509c-252">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e509c-252">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e509c-253">Usługi, takie jak kontekst bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-253">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e509c-254">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="e509c-254">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e509c-255">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="e509c-255">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e509c-256">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="e509c-256">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e509c-257">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="e509c-257">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e509c-258">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="e509c-258">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e509c-259">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e509c-259">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e509c-260">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e509c-260">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e509c-261">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-261">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e509c-262">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e509c-262">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="e509c-263">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-263">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e509c-264">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e509c-264">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e509c-265">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="e509c-265">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e509c-266">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="e509c-266">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e509c-267">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-267">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e509c-268">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="e509c-268">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="e509c-269">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="e509c-269">Test the app</span></span>

1. <span data-ttu-id="e509c-270">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-270">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="e509c-271">Jeśli pojawi się następujący komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="e509c-271">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="e509c-272">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e509c-272">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="e509c-273">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="e509c-273">Test the **Create** link.</span></span>

   ![Tworzenie strony](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="e509c-275">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="e509c-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e509c-276">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="e509c-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e509c-277">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="e509c-277">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="e509c-278">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="e509c-278">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e509c-279">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="e509c-279">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e509c-280">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e509c-280">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e509c-281">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e509c-281">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e509c-282">W tej sekcji dodano klasy do zarządzania filmami.</span><span class="sxs-lookup"><span data-stu-id="e509c-282">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="e509c-283">Klasy modelu aplikacji używają [Entity Framework Core (Ef Core)](/ef/core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-283">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e509c-284">EF Core to Mapowanie obiektowo-relacyjne (O/RM), które upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-284">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="e509c-285">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="e509c-285">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e509c-286">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="e509c-286">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e509c-287">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e509c-287">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e509c-288">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="e509c-288">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-289">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-289">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-290">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-290">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e509c-291">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="e509c-291">Name the folder *Models*.</span></span>

<span data-ttu-id="e509c-292">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="e509c-292">Right-click the *Models* folder.</span></span> <span data-ttu-id="e509c-293">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="e509c-293">Select **Add** > **Class**.</span></span> <span data-ttu-id="e509c-294">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="e509c-294">Name the class **Movie**.</span></span>

<span data-ttu-id="e509c-295">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-295">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-296">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-296">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-297">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-297">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-298">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-298">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-299">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-299">With this attribute:</span></span>

  * <span data-ttu-id="e509c-300">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-300">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-301">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-301">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e509c-302">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-302">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-303">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-303">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e509c-304">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="e509c-304">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e509c-305">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e509c-305">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e509c-306">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-307">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-308">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-309">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-310">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-310">With this attribute:</span></span>

  * <span data-ttu-id="e509c-311">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-312">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e509c-313">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e509c-314">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="e509c-314">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e509c-315">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e509c-315">Add a database context class</span></span>

* <span data-ttu-id="e509c-316">W projekcie *Razor PagesMovie* Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="e509c-316">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="e509c-317">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="e509c-317">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e509c-318">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e509c-318">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e509c-319">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e509c-319">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e509c-320">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="e509c-320">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e509c-321">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="e509c-321">Add a database connection string</span></span>

<span data-ttu-id="e509c-322">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="e509c-322">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e509c-323">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e509c-323">Register the database context</span></span>

<span data-ttu-id="e509c-324">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e509c-324">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e509c-325">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e509c-325">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-326">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e509c-327">W **oknie narzędzia rozwiązania** kliknij projekt **Razor PagesMovie** , a następnie wybierz pozycję **Dodaj** > **Nowy folder.**... Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="e509c-327">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="e509c-328">Kliknij prawym przyciskiem myszy folder *modele* , a następnie wybierz polecenie **Dodaj** > **nowy plik.**...</span><span class="sxs-lookup"><span data-stu-id="e509c-328">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="e509c-329">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="e509c-329">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e509c-330">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="e509c-330">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e509c-331">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="e509c-331">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e509c-332">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-332">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e509c-333">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-333">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-334">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-334">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-335">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-335">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-336">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-336">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-337">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-337">With this attribute:</span></span>

  * <span data-ttu-id="e509c-338">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-338">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-339">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-339">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e509c-340">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-340">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e509c-341">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-341">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e509c-342">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="e509c-342">Scaffold the movie model</span></span>

<span data-ttu-id="e509c-343">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="e509c-343">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e509c-344">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="e509c-344">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-345">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-345">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-346">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="e509c-346">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e509c-347">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-347">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e509c-348">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="e509c-348">Name the folder *Movies*.</span></span>

<span data-ttu-id="e509c-349">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-349">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="e509c-351">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-351">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="e509c-353">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e509c-353">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e509c-354">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="e509c-354">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e509c-355">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę z Razor PagesMovie.**Modele**. Razor PagesMovieContext do Razor PagesMovie.**Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e509c-355">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e509c-356">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="e509c-356">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e509c-357">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="e509c-357">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e509c-358">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-358">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/3/arp.png)

<span data-ttu-id="e509c-360">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-360">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-361">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-361">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e509c-362">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e509c-362">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e509c-363">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-363">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e509c-364">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-364">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e509c-365">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="e509c-365">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e509c-366">Opcja</span><span class="sxs-lookup"><span data-stu-id="e509c-366">Option</span></span>               | <span data-ttu-id="e509c-367">Opis</span><span class="sxs-lookup"><span data-stu-id="e509c-367">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e509c-368">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="e509c-368">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e509c-369">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="e509c-369">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e509c-370">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="e509c-370">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e509c-371">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="e509c-371">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e509c-372">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron</span><span class="sxs-lookup"><span data-stu-id="e509c-372">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e509c-373">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="e509c-373">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e509c-374">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e509c-374">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e509c-375">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="e509c-375">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e509c-376">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="e509c-376">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e509c-377">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="e509c-377">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e509c-378">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="e509c-378">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-379">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-379">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e509c-380">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="e509c-380">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e509c-381">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-381">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e509c-382">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="e509c-382">Name the folder *Movies*.</span></span>

<span data-ttu-id="e509c-383">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowe tworzenie szkieletu..**..</span><span class="sxs-lookup"><span data-stu-id="e509c-383">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="e509c-385">W oknie dialogowym **Nowa rusztowania** wybierz pozycję **Razor strony z Entity Framework (CRUD)** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="e509c-385">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e509c-387">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e509c-387">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e509c-388">W menu rozwijanym **Klasa modelu** wybierz lub wpisz **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="e509c-388">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e509c-389">W wierszu **klasy kontekstu danych** wpisz nazwę nowej klasy, Razor PagesMovie.**Dane**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e509c-389">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e509c-390">[Ta zmiana](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="e509c-390">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e509c-391">Tworzy klasę kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="e509c-391">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e509c-392">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-392">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="e509c-394">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-394">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="e509c-395">Dodawanie narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="e509c-395">Add EF tools</span></span>

<span data-ttu-id="e509c-396">Uruchom następujące interfejs wiersza polecenia platformy .NET Core polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-396">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="e509c-397">Poprzednie polecenie dodaje Entity Framework Core narzędzia dla interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e509c-397">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="e509c-398">Aby uzyskać więcej informacji, zobacz [Entity Framework Core Tools Reference-interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="e509c-398">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e509c-399">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="e509c-399">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e509c-400">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="e509c-400">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e509c-401">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="e509c-401">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e509c-402">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="e509c-402">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e509c-403">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="e509c-403">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-405">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-405">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e509c-406">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-406">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e509c-407">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-407">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e509c-408">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="e509c-408">Updated</span></span>

* <span data-ttu-id="e509c-409">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-409">*Startup.cs*</span></span>

<span data-ttu-id="e509c-410">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-410">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-411">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-411">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e509c-412">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-412">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e509c-413">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-413">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e509c-414">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-414">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e509c-415">Po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="e509c-415">Updated</span></span>

* <span data-ttu-id="e509c-416">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-416">*Startup.cs*</span></span>

<span data-ttu-id="e509c-417">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-417">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-418">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-418">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e509c-419">Proces tworzenia szkieletu tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-419">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e509c-420">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-420">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e509c-421">Utworzone pliki zostały wyjaśnione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-421">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e509c-422">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="e509c-422">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-424">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="e509c-424">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e509c-425">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-425">Add an initial migration.</span></span>
* <span data-ttu-id="e509c-426">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-426">Update the database with the initial migration.</span></span>

<span data-ttu-id="e509c-427">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="e509c-427">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e509c-429">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="e509c-429">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e509c-430">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-430">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e509c-431">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e509c-431">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e509c-432">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="e509c-432">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e509c-433">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="e509c-433">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e509c-434">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="e509c-434">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e509c-435">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="e509c-435">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e509c-436">Polecenie migrations generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-436">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e509c-437">Schemat jest oparty na modelu określonym w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="e509c-437">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e509c-438">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-438">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e509c-439">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="e509c-439">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e509c-440">`update`Polecenie uruchamia `Up` metodę w migracjach, które nie zostały zastosowane.</span><span class="sxs-lookup"><span data-stu-id="e509c-440">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e509c-441">W takim przypadku program `update` uruchamia `Up` metodę w pliku  *migrations/ \<time-stamp> _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-441">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-442">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e509c-443">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="e509c-443">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e509c-444">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e509c-444">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e509c-445">Usługi, takie jak kontekst kontekstu bazy danych EF Core, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-445">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e509c-446">Składniki, które wymagają tych usług, takie jak Razor strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="e509c-446">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="e509c-447">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="e509c-447">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e509c-448">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="e509c-448">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e509c-449">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="e509c-449">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e509c-450">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="e509c-450">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e509c-451">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e509c-451">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e509c-452">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e509c-452">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e509c-453">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-453">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e509c-454">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e509c-454">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e509c-455">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-455">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e509c-456">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e509c-456">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e509c-457">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="e509c-457">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e509c-458">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="e509c-458">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e509c-459">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-459">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e509c-460">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="e509c-460">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e509c-461">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="e509c-461">Test the app</span></span>

* <span data-ttu-id="e509c-462">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-462">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e509c-463">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="e509c-463">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e509c-464">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e509c-464">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e509c-465">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="e509c-465">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="e509c-467">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="e509c-467">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e509c-468">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="e509c-468">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e509c-469">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="e509c-469">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e509c-470">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="e509c-470">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e509c-471">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="e509c-471">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e509c-472">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e509c-472">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e509c-473">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e509c-473">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e509c-474">W tej sekcji są dodawane klasy do zarządzania filmami w wieloplatformowej [bazie danych SQLite](https://www.sqlite.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="e509c-474">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e509c-475">Aplikacje utworzone na podstawie szablonu ASP.NET Core korzystają z bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="e509c-475">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e509c-476">Klasy modelu aplikacji są używane z [Entity Framework Core (Ef Core)](/ef/core) ([dostawca bazy danych programu SQLite EF Core](/ef/core/providers/sqlite)) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-476">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e509c-477">EF Core to struktura obiektów mapowania relacyjnego (ORM), która upraszcza dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-477">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e509c-478">Klasy modelu są znane jako klasy POCO (z "zwykłych, starych obiektów CLR"), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="e509c-478">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e509c-479">Definiują właściwości danych przechowywanych w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="e509c-479">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e509c-480">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e509c-480">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e509c-481">Dodawanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="e509c-481">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-482">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-482">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-483">Kliknij prawym przyciskiem myszy projekt **Razor PagesMovie** > **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-483">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e509c-484">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="e509c-484">Name the folder *Models*.</span></span>

<span data-ttu-id="e509c-485">Kliknij prawym przyciskiem myszy folder *modele* .</span><span class="sxs-lookup"><span data-stu-id="e509c-485">Right-click the *Models* folder.</span></span> <span data-ttu-id="e509c-486">Wybierz pozycję **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="e509c-486">Select **Add** > **Class**.</span></span> <span data-ttu-id="e509c-487">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="e509c-487">Name the class **Movie**.</span></span>

<span data-ttu-id="e509c-488">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-488">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-489">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-489">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-490">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-490">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-491">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-491">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-492">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-492">With this attribute:</span></span>

  * <span data-ttu-id="e509c-493">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-493">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-494">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-494">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e509c-495">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-495">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e509c-497">Dodaj folder o nazwie *models*.</span><span class="sxs-lookup"><span data-stu-id="e509c-497">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e509c-498">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e509c-498">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e509c-499">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-500">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-501">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-502">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-503">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-503">With this attribute:</span></span>

  * <span data-ttu-id="e509c-504">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-505">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e509c-506">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e509c-507">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="e509c-507">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e509c-508">Dodaj klasę kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e509c-508">Add a database context class</span></span>

<span data-ttu-id="e509c-509">W Razor projekcie PagesMovie Utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="e509c-509">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="e509c-510">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="e509c-510">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e509c-511">Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e509c-511">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e509c-512">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e509c-512">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e509c-513">Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="e509c-513">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e509c-514">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="e509c-514">Add a database connection string</span></span>

<span data-ttu-id="e509c-515">Dodaj parametry połączenia do *appsettings.json* pliku, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="e509c-515">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="e509c-516">Dodaj wymagane pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="e509c-516">Add required NuGet packages</span></span>

<span data-ttu-id="e509c-517">Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="e509c-517">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="e509c-518">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="e509c-518">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e509c-519">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e509c-519">Register the database context</span></span>

<span data-ttu-id="e509c-520">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e509c-520">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e509c-521">Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e509c-521">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="e509c-522">Kompiluj projekt jako sprawdzenie pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="e509c-522">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-523">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-523">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e509c-524">W **oknie narzędzia rozwiązania** kliknij projekt *Razor PagesMovie* , a następnie wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-524">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e509c-525">Nazwij *modele* folderów.</span><span class="sxs-lookup"><span data-stu-id="e509c-525">Name the folder *Models*.</span></span>
* <span data-ttu-id="e509c-526">Kliknij przycisk Control ( *modele* ), a następnie wybierz pozycję **Dodaj** > **nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="e509c-526">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e509c-527">W oknie dialogowym **nowy plik** :</span><span class="sxs-lookup"><span data-stu-id="e509c-527">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e509c-528">W lewym okienku wybierz pozycję **Ogólne** .</span><span class="sxs-lookup"><span data-stu-id="e509c-528">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e509c-529">Wybierz **pustą klasę** w środkowym okienku.</span><span class="sxs-lookup"><span data-stu-id="e509c-529">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e509c-530">Nazwij **film** klasy i wybierz pozycję **Nowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-530">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e509c-531">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="e509c-531">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e509c-532">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="e509c-532">The `Movie` class contains:</span></span>

* <span data-ttu-id="e509c-533">`ID`Pole jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="e509c-533">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e509c-534">`[DataType(DataType.Date)]`: Atrybut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-534">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e509c-535">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="e509c-535">With this attribute:</span></span>

  * <span data-ttu-id="e509c-536">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="e509c-536">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e509c-537">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="e509c-537">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e509c-538">[Adnotacje DataAnnotations](xref:System.ComponentModel.DataAnnotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="e509c-538">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="e509c-539">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-539">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e509c-540">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="e509c-540">Scaffold the movie model</span></span>

<span data-ttu-id="e509c-541">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="e509c-541">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e509c-542">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="e509c-542">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-543">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-544">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="e509c-544">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e509c-545">Kliknij prawym przyciskiem myszy folder *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-545">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e509c-546">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="e509c-546">Name the folder *Movies*.</span></span>

<span data-ttu-id="e509c-547">Kliknij prawym przyciskiem myszy folder *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-547">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/sca.png)

<span data-ttu-id="e509c-549">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-549">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffold.png)

<span data-ttu-id="e509c-551">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e509c-551">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e509c-552">Z listy rozwijanej **Klasa modelu** wybierz pozycję **film ( Razor PagesMovie. models)**.</span><span class="sxs-lookup"><span data-stu-id="e509c-552">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e509c-553">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zaakceptuj wygenerowaną nazwę **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e509c-553">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e509c-554">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-554">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arp.png)

<span data-ttu-id="e509c-556">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-556">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e509c-557">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e509c-557">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e509c-558">Otwórz okno polecenia w katalogu projektu, który zawiera pliki *program.cs*, *Startup.cs* i *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e509c-558">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="e509c-559">**Dla systemu Windows**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-559">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e509c-560">**W przypadku systemów macOS i Linux**: Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e509c-560">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e509c-561">W poniższej tabeli przedstawiono szczegóły ASP.NET Core opcje generatora kodu:</span><span class="sxs-lookup"><span data-stu-id="e509c-561">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e509c-562">Opcja</span><span class="sxs-lookup"><span data-stu-id="e509c-562">Option</span></span>               | <span data-ttu-id="e509c-563">Opis</span><span class="sxs-lookup"><span data-stu-id="e509c-563">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e509c-564">Nazwa modelu.</span><span class="sxs-lookup"><span data-stu-id="e509c-564">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e509c-565">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="e509c-565">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e509c-566">Użyj układu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="e509c-566">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e509c-567">Ścieżka względna folderu wyjściowego do tworzenia widoków.</span><span class="sxs-lookup"><span data-stu-id="e509c-567">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e509c-568">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron</span><span class="sxs-lookup"><span data-stu-id="e509c-568">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e509c-569">Użyj `-h` opcji, aby uzyskać pomoc dotyczącą `aspnet-codegenerator razorpage` polecenia:</span><span class="sxs-lookup"><span data-stu-id="e509c-569">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e509c-570">Aby uzyskać więcej informacji, zobacz [dotnet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e509c-570">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e509c-571">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-571">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e509c-572">Utwórz folder *stron/filmów* :</span><span class="sxs-lookup"><span data-stu-id="e509c-572">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e509c-573">Kliknij przycisk sterowania w folderze *strony* > **Dodaj** > **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="e509c-573">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e509c-574">Nazwij foldery *filmów*.</span><span class="sxs-lookup"><span data-stu-id="e509c-574">Name the folder *Movies*.</span></span>

<span data-ttu-id="e509c-575">Kliknij przycisk sterowania w folderze *strony/filmy* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="e509c-575">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/scaMac.png)

<span data-ttu-id="e509c-577">W oknie dialogowym **Dodawanie nowej szkieletu** wybierz pozycję **Razor strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-577">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e509c-579">Wypełnij okno dialogowe **Dodawanie Razor stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e509c-579">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e509c-580">Z listy rozwijanej **Klasa modelu** wybierz lub wpisz **film**.</span><span class="sxs-lookup"><span data-stu-id="e509c-580">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="e509c-581">W wierszu **klasy kontekstu danych** wpisz wybierz **Razor PagesMovieContext** to spowoduje utworzenie nowej klasy kontekstu bazy danych z poprawną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="e509c-581">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="e509c-582">W takim przypadku będzie to **Razor PagesMovie. models. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e509c-582">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e509c-583">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="e509c-583">Select **Add**.</span></span>

![Obraz z poprzednich instrukcji.](model/_static/arpMac.png)

<span data-ttu-id="e509c-585">*appsettings.json* Plik zostanie zaktualizowany przy użyciu parametrów połączenia używanych do nawiązywania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-585">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="e509c-586">Proces tworzenia szkieletu tworzy i aktualizuje następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="e509c-586">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e509c-587">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="e509c-587">Files created</span></span>

* <span data-ttu-id="e509c-588">*Strony/filmy*: Tworzenie, usuwanie, szczegóły, edytowanie i Index .</span><span class="sxs-lookup"><span data-stu-id="e509c-588">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e509c-589">*Dane/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-589">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e509c-590">Zaktualizowano plik</span><span class="sxs-lookup"><span data-stu-id="e509c-590">File updated</span></span>

* <span data-ttu-id="e509c-591">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e509c-591">*Startup.cs*</span></span>

<span data-ttu-id="e509c-592">Pliki utworzone i zaktualizowane zostały omówione w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e509c-592">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e509c-593">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="e509c-593">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-594">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-594">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e509c-595">W tej sekcji konsola Menedżera pakietów (PMC) służy do:</span><span class="sxs-lookup"><span data-stu-id="e509c-595">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e509c-596">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-596">Add an initial migration.</span></span>
* <span data-ttu-id="e509c-597">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-597">Update the database with the initial migration.</span></span>

<span data-ttu-id="e509c-598">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="e509c-598">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e509c-600">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="e509c-600">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e509c-601">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-601">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e509c-602">Schemat jest oparty na modelu określonym w `DbContext` , w pliku *Razor PagesMovieContext.cs* .</span><span class="sxs-lookup"><span data-stu-id="e509c-602">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="e509c-603">`InitialCreate`Argument jest używany do nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="e509c-603">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e509c-604">Można użyć dowolnej nazwy, ale według Konwencji Nazwa opisująca migrację jest używana.</span><span class="sxs-lookup"><span data-stu-id="e509c-604">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e509c-605">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e509c-605">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e509c-606">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="e509c-606">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e509c-607">`Up`Metoda tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-607">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e509c-608">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-608">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e509c-609">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e509c-609">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="e509c-610">Powyższe polecenia generują następujące ostrzeżenie: "nie określono typu dla kolumny dziesiętnej" price "w typie jednostki" Movie ".</span><span class="sxs-lookup"><span data-stu-id="e509c-610">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e509c-611">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="e509c-611">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e509c-612">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="e509c-612">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e509c-613">Zignoruj ostrzeżenie, ponieważ zostanie ono rozkierowane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="e509c-613">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e509c-614">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e509c-614">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e509c-615">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="e509c-615">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e509c-616">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e509c-616">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e509c-617">Usługi (takie jak kontekst kontekstu bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e509c-617">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e509c-618">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="e509c-618">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e509c-619">Kod konstruktora, który pobiera wystąpienie kontekstu contextB kontekstu bazy danych jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="e509c-619">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e509c-620">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst kontekstu bazy danych i zarejestrował go z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="e509c-620">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e509c-621">Zapoznaj się z tą `Startup.ConfigureServices` metodą.</span><span class="sxs-lookup"><span data-stu-id="e509c-621">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e509c-622">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="e509c-622">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e509c-623">`RazorPagesMovieContext`Współrzędne EF Core funkcje, takie jak tworzenie, Odczyt, aktualizowanie i usuwanie, dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e509c-623">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="e509c-624">Kontekst danych ( `RazorPagesMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span><span class="sxs-lookup"><span data-stu-id="e509c-624">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e509c-625">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-625">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e509c-626">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e509c-626">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e509c-627">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e509c-627">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e509c-628">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e509c-628">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e509c-629">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) .</span><span class="sxs-lookup"><span data-stu-id="e509c-629">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e509c-630">W przypadku lokalnego tworzenia [system konfiguracji](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="e509c-630">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e509c-631">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="e509c-631">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e509c-632">Zapoznaj się z tą `Up` metodą.</span><span class="sxs-lookup"><span data-stu-id="e509c-632">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e509c-633">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="e509c-633">Test the app</span></span>

* <span data-ttu-id="e509c-634">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="e509c-634">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e509c-635">Jeśli wystąpi błąd:</span><span class="sxs-lookup"><span data-stu-id="e509c-635">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e509c-636">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e509c-636">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e509c-637">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="e509c-637">Test the **Create** link.</span></span>

  ![Tworzenie strony](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e509c-639">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="e509c-639">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e509c-640">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="e509c-640">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e509c-641">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="e509c-641">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e509c-642">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="e509c-642">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e509c-643">W następnym samouczku objaśniono pliki utworzone przez tworzenie szkieletu.</span><span class="sxs-lookup"><span data-stu-id="e509c-643">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e509c-644">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e509c-644">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e509c-645">[Poprzedni:](xref:tutorials/razor-pages/razor-pages-start) 
>  wprowadzenie [Dalej: Rusztowania Razor Strony](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e509c-645">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
