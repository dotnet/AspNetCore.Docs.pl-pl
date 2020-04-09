---
title: Dodawanie modelu do ASP.NET aplikacji Core MVC
author: rick-anderson
description: Dodaj model do prostej aplikacji ASP.NET Core.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434515"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="53d96-103">Dodawanie modelu do ASP.NET aplikacji Core MVC</span><span class="sxs-lookup"><span data-stu-id="53d96-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="53d96-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Tom [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="53d96-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="53d96-105">W tej sekcji należy dodać klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="53d96-106">Zajęcia te będą częścią aplikacji **M**VC "**M**odel".</span><span class="sxs-lookup"><span data-stu-id="53d96-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="53d96-107">Te klasy są używane z [entity framework core](/ef/core) (EF Core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="53d96-108">EF Core jest struktura mapowania obiektowego (ORM), która upraszcza kod dostępu do danych, który należy napisać.</span><span class="sxs-lookup"><span data-stu-id="53d96-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="53d96-109">Klasy modelu, które tworzysz są znane jako klasy POCO (z **P**lain **O**ld **C**LR **O**bjects), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="53d96-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="53d96-110">Po prostu definiują właściwości danych, które będą przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="53d96-111">W tym samouczku najpierw napisz klasy modelu, a EF Core tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="53d96-112">Dodawanie klasy modelu danych</span><span class="sxs-lookup"><span data-stu-id="53d96-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-114">Kliknij prawym przyciskiem myszy folder *Modele* > **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="53d96-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="53d96-115">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="53d96-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53d96-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53d96-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="53d96-117">Dodaj plik o nazwie *Movie.cs* do folderu *Modele.*</span><span class="sxs-lookup"><span data-stu-id="53d96-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53d96-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53d96-119">Kliknij prawym przyciskiem myszy folder *Modele* > **Dodaj** > **nową klasę** > **pustą klasę**.</span><span class="sxs-lookup"><span data-stu-id="53d96-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="53d96-120">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="53d96-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="53d96-121">Zaktualizuj plik *Movie.cs* o następujący kod:</span><span class="sxs-lookup"><span data-stu-id="53d96-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="53d96-122">Klasa `Movie` zawiera `Id` pole, które jest wymagane przez bazę danych dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="53d96-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="53d96-123">Atrybut <xref:System.ComponentModel.DataAnnotations.DataType> na `ReleaseDate` określa typ danych (`Date`).</span><span class="sxs-lookup"><span data-stu-id="53d96-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="53d96-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="53d96-124">With this attribute:</span></span>

* <span data-ttu-id="53d96-125">Użytkownik nie musi wprowadzać informacji o godzinie w polu daty.</span><span class="sxs-lookup"><span data-stu-id="53d96-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="53d96-126">Wyświetlana jest tylko data, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="53d96-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="53d96-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="53d96-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="53d96-128">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="53d96-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-130">Z menu **Narzędzia** wybierz pozycję Konsola Menedżera **pakietów** > Programu NuGet Package **Manager** (PMC).</span><span class="sxs-lookup"><span data-stu-id="53d96-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="53d96-132">W pmc uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="53d96-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="53d96-133">Poprzednie polecenie dodaje ef core dostawcy programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="53d96-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="53d96-134">Pakiet dostawcy instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="53d96-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="53d96-135">Dodatkowe pakiety są instalowane automatycznie w kroku szkieletu w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="53d96-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53d96-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53d96-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53d96-137">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="53d96-138">Z menu **Projekt** wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="53d96-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="53d96-139">W polu **Wyszukiwanie** w prawym `Microsoft.EntityFrameworkCore.SQLite` górnym rogu wprowadź i naciśnij klawisz **Return,** aby wyszukać.</span><span class="sxs-lookup"><span data-stu-id="53d96-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="53d96-140">Wybierz pasujący pakiet NuGet i naciśnij przycisk **Dodaj pakiet.**</span><span class="sxs-lookup"><span data-stu-id="53d96-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Dodaj pakiet NuGet Core framework framework entity framework](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="53d96-142">Zostanie wyświetlone okno dialogowe Wybierz `MvcMovie` **projekty** z wybranym projektem.</span><span class="sxs-lookup"><span data-stu-id="53d96-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="53d96-143">Naciśnij przycisk **Ok.**</span><span class="sxs-lookup"><span data-stu-id="53d96-143">Press the **Ok** button.</span></span>

<span data-ttu-id="53d96-144">Zostanie wyświetlone okno dialogowe **Akceptacja licencji.**</span><span class="sxs-lookup"><span data-stu-id="53d96-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="53d96-145">Przejrzyj licencje zgodnie z potrzebami, a następnie kliknij przycisk **Zaakceptuj.**</span><span class="sxs-lookup"><span data-stu-id="53d96-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="53d96-146">Powtórz powyższe kroki, aby zainstalować następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="53d96-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="53d96-147">Tworzenie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="53d96-147">Create a database context class</span></span>

<span data-ttu-id="53d96-148">Klasa kontekstu bazy danych jest potrzebna do koordynowania funkcji EF `Movie` Core (Tworzenie, Odczyt, Aktualizacja, Usuwanie) dla modelu.</span><span class="sxs-lookup"><span data-stu-id="53d96-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="53d96-149">Kontekst bazy danych jest uzyskiwane z [microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i określa jednostki do uwzględnienia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="53d96-150">Utwórz folder *Dane.*</span><span class="sxs-lookup"><span data-stu-id="53d96-150">Create a *Data* folder.</span></span>

<span data-ttu-id="53d96-151">Dodaj plik *Data/MvcMovieContext.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="53d96-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="53d96-152">Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="53d96-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="53d96-153">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="53d96-154">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="53d96-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="53d96-155">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="53d96-155">Register the database context</span></span>

<span data-ttu-id="53d96-156">ASP.NET Core jest zbudowany z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="53d96-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="53d96-157">Usługi (takie jak kontekst ef core db) muszą być zarejestrowane w DI podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53d96-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="53d96-158">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="53d96-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="53d96-159">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="53d96-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="53d96-160">W tej sekcji można zarejestrować kontekst bazy danych z kontenerem DI.</span><span class="sxs-lookup"><span data-stu-id="53d96-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="53d96-161">Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="53d96-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="53d96-162">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod w :</span><span class="sxs-lookup"><span data-stu-id="53d96-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-163">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-164">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="53d96-165">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="53d96-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="53d96-166">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="53d96-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="53d96-167">Dodawanie ciągu połączenia bazy danych</span><span class="sxs-lookup"><span data-stu-id="53d96-167">Add a database connection string</span></span>

<span data-ttu-id="53d96-168">Dodaj parametry połączenia do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="53d96-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-169">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-170">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="53d96-171">Skompiluj projekt jako sprawdzanie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="53d96-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="53d96-172">Strony filmów rusztowania</span><span class="sxs-lookup"><span data-stu-id="53d96-172">Scaffold movie pages</span></span>

<span data-ttu-id="53d96-173">Narzędzie rusztowania służy do tworzenia stron Tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="53d96-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-174">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-175">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Kontrolery* **> Dodaj > nowy element szkieletu**.</span><span class="sxs-lookup"><span data-stu-id="53d96-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![widok powyższego kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="53d96-177">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC z widokami, używając programu Entity Framework > Add**.</span><span class="sxs-lookup"><span data-stu-id="53d96-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="53d96-179">Wykonaj okno dialogowe **Dodawanie kontrolera:**</span><span class="sxs-lookup"><span data-stu-id="53d96-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="53d96-180">**Klasa modelu:** *Film (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="53d96-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="53d96-181">**Klasa kontekstu danych:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="53d96-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Dodawanie kontekstu danych](adding-model/_static/dc3.png)

* <span data-ttu-id="53d96-183">**Widoki:** Sprawdzanie wartości domyślnej każdej opcji</span><span class="sxs-lookup"><span data-stu-id="53d96-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="53d96-184">**Nazwa kontrolera:** Zachowaj domyślny *kontroler MoviesController*</span><span class="sxs-lookup"><span data-stu-id="53d96-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="53d96-185">Wybierz **pozycję Dodaj**</span><span class="sxs-lookup"><span data-stu-id="53d96-185">Select **Add**</span></span>

<span data-ttu-id="53d96-186">Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="53d96-186">Visual Studio creates:</span></span>

* <span data-ttu-id="53d96-187">Kontroler filmów (*Kontrolery/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="53d96-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="53d96-188">Pliki widoku razor dla stron Tworzenie, Usuwanie, Szczegóły, Edycja i Indeks *(Widoki/Filmy/\*.cshtml)*</span><span class="sxs-lookup"><span data-stu-id="53d96-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="53d96-189">Automatyczne tworzenie tych plików jest znany jako *rusztowania*.</span><span class="sxs-lookup"><span data-stu-id="53d96-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="53d96-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53d96-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="53d96-191">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="53d96-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="53d96-192">W systemie Linux wyeksportuj ścieżkę narzędzia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="53d96-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="53d96-193">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="53d96-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53d96-194">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53d96-195">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="53d96-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="53d96-196">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="53d96-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="53d96-197">Nie można jeszcze użyć szkieletu strony, ponieważ baza danych nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="53d96-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="53d96-198">Jeśli uruchomisz aplikację i klikniesz łącze **Movie App,** zostanie wyświetlony *komunikat o błędzie Nie można otworzyć bazy danych* lub brak takiej *tabeli:* Komunikat o błędzie filmu.</span><span class="sxs-lookup"><span data-stu-id="53d96-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="53d96-199">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="53d96-199">Initial migration</span></span>

<span data-ttu-id="53d96-200">Użyj funkcji [Migracje rdzenia](xref:data/ef-mvc/migrations) EF, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="53d96-201">Migracje to zestaw narzędzi, które umożliwiają tworzenie i aktualizowanie bazy danych w celu dopasowania do modelu danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-202">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-203">Z menu **Narzędzia** wybierz pozycję Konsola Menedżera **pakietów** > Programu NuGet Package **Manager** (PMC).</span><span class="sxs-lookup"><span data-stu-id="53d96-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="53d96-204">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="53d96-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="53d96-205">`Add-Migration InitialCreate`: Generuje plik *migracji/{timestamp}_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="53d96-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="53d96-206">Argument `InitialCreate` jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="53d96-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="53d96-207">Można użyć dowolnej nazwy, ale zgodnie z konwencją jest zaznaczona nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="53d96-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="53d96-208">Ponieważ jest to pierwsza migracja, wygenerowana klasa zawiera kod do utworzenia schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="53d96-209">Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie.</span><span class="sxs-lookup"><span data-stu-id="53d96-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="53d96-210">`Update-Database`: Aktualizuje bazę danych do najnowszej migracji, którą utworzyło poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="53d96-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="53d96-211">To polecenie `Up` uruchamia metodę w pliku *Migrations/{time-stamp}_InitialCreate.cs,* który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="53d96-212">Polecenie aktualizacji bazy danych generuje następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="53d96-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="53d96-213">Nie określono typu dla kolumny dziesiętnej "Cena" dla typu encji "Film".</span><span class="sxs-lookup"><span data-stu-id="53d96-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="53d96-214">Spowoduje to, że wartości będą dyskretnie obcinane, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="53d96-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="53d96-215">Jawnie określ typ kolumny serwera SQL, który może pomieścić wszystkie wartości za pomocą "HasColumnType()".</span><span class="sxs-lookup"><span data-stu-id="53d96-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="53d96-216">Możesz zignorować to ostrzeżenie, zostanie ono naprawione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="53d96-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-217">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="53d96-218">Uruchom następujące polecenia .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="53d96-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="53d96-219">`ef migrations add InitialCreate`: Generuje plik *migracji/{timestamp}_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="53d96-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="53d96-220">Argument `InitialCreate` jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="53d96-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="53d96-221">Można użyć dowolnej nazwy, ale zgodnie z konwencją jest zaznaczona nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="53d96-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="53d96-222">Ponieważ jest to pierwsza migracja, wygenerowana klasa zawiera kod do utworzenia schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="53d96-223">Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie (w pliku *Data/MvcMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="53d96-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="53d96-224">`ef database update`: Aktualizuje bazę danych do najnowszej migracji, którą utworzyło poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="53d96-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="53d96-225">To polecenie `Up` uruchamia metodę w pliku *Migrations/{time-stamp}_InitialCreate.cs,* który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="53d96-226">Klasa InitialCreate</span><span class="sxs-lookup"><span data-stu-id="53d96-226">The InitialCreate class</span></span>

<span data-ttu-id="53d96-227">Sprawdź plik *migracji/{sygnatura czasowa}_InitialCreate.cs:*</span><span class="sxs-lookup"><span data-stu-id="53d96-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="53d96-228">Metoda `Up` tworzy tabelę Movie `Id` i konfiguruje jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="53d96-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="53d96-229">Metoda `Down` przywraca zmiany schematu wprowadzone `Up` przez migrację.</span><span class="sxs-lookup"><span data-stu-id="53d96-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="53d96-230">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="53d96-230">Test the app</span></span>

* <span data-ttu-id="53d96-231">Uruchom aplikację i kliknij łącze **Movie App.**</span><span class="sxs-lookup"><span data-stu-id="53d96-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="53d96-232">Jeśli zostanie otrzymasz wyjątek podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="53d96-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-233">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-234">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="53d96-235">Prawdopodobnie przegapiłeś [krok migracji](#migration).</span><span class="sxs-lookup"><span data-stu-id="53d96-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="53d96-236">Przetestuj stronę **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="53d96-236">Test the **Create** page.</span></span> <span data-ttu-id="53d96-237">Wprowadzanie i przesyłanie danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="53d96-238">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="53d96-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="53d96-239">Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana.</span><span class="sxs-lookup"><span data-stu-id="53d96-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="53d96-240">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="53d96-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="53d96-241">Testowanie stron **Edycja,** **Szczegóły**i **Usuń.**</span><span class="sxs-lookup"><span data-stu-id="53d96-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="53d96-242">Wstrzyknięcie zależności w sterowniku</span><span class="sxs-lookup"><span data-stu-id="53d96-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-243">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-244">Otwórz plik *Controllers/MoviesController.cs* i sprawdź konstruktor:</span><span class="sxs-lookup"><span data-stu-id="53d96-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="53d96-245">Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) `MvcMovieContext`aby wstrzyknąć kontekst bazy danych ( ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="53d96-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="53d96-246">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="53d96-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-247">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="53d96-248">Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) `MvcMovieContext`aby wstrzyknąć kontekst bazy danych ( ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="53d96-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="53d96-249">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="53d96-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="53d96-250">Użyj SQLite do tworzenia, SQL Server dla produkcji</span><span class="sxs-lookup"><span data-stu-id="53d96-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="53d96-251">Po wybraniu SQLite, szablon wygenerowany kod jest gotowy do rozwoju.</span><span class="sxs-lookup"><span data-stu-id="53d96-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="53d96-252">Poniższy kod pokazuje, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> jak wstrzyknąć do uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="53d96-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="53d96-253">`IWebHostEnvironment`jest wstrzykiwany, więc `ConfigureServices` można użyć SQLite w rozwoju i SQL Server w produkcji.</span><span class="sxs-lookup"><span data-stu-id="53d96-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="53d96-254">Silnie wpisane modele @model i słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="53d96-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="53d96-255">Wcześniej w tym samouczku, widziałeś, jak kontroler może `ViewData` przekazać dane lub obiekty do widoku przy użyciu słownika.</span><span class="sxs-lookup"><span data-stu-id="53d96-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="53d96-256">Słownik `ViewData` jest obiektem dynamicznym, który zapewnia wygodny późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="53d96-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="53d96-257">MVC zapewnia również możliwość przekazywania silnie wpisane obiekty modelu do widoku.</span><span class="sxs-lookup"><span data-stu-id="53d96-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="53d96-258">To silnie typizowane podejście umożliwia sprawdzanie kodu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="53d96-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="53d96-259">Mechanizm rusztowania używane to podejście (oznacza to, przekazując `MoviesController` model silnie typizowane) z klasy i widoków.</span><span class="sxs-lookup"><span data-stu-id="53d96-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="53d96-260">Sprawdź wygenerowaną `Details` metodę w pliku *Controllers/MoviesController.cs:*</span><span class="sxs-lookup"><span data-stu-id="53d96-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="53d96-261">Parametr `id` jest zazwyczaj przekazywany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="53d96-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="53d96-262">Na `https://localhost:5001/movies/details/1` przykład zestawy:</span><span class="sxs-lookup"><span data-stu-id="53d96-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="53d96-263">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="53d96-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="53d96-264">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="53d96-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="53d96-265">Identyfikator do 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="53d96-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="53d96-266">Można również przekazać `id` w ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="53d96-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="53d96-267">Parametr `id` jest zdefiniowany jako typ`int?` [nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( ) w przypadku, gdy wartość identyfikatora nie jest podana.</span><span class="sxs-lookup"><span data-stu-id="53d96-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="53d96-268">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przekazywane `FirstOrDefaultAsync` do wybierania jednostek filmu, które pasują do danych trasy lub wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="53d96-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="53d96-269">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest `Details` przekazywane do widoku:</span><span class="sxs-lookup"><span data-stu-id="53d96-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="53d96-270">Sprawdź zawartość pliku *Views/Movies/Details.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="53d96-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="53d96-271">Instrukcja `@model` w górnej części pliku widoku określa typ obiektu, który oczekuje widoku.</span><span class="sxs-lookup"><span data-stu-id="53d96-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="53d96-272">Podczas tworzenia kontrolera filmu `@model` uwzględniono następującą instrukcję:</span><span class="sxs-lookup"><span data-stu-id="53d96-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="53d96-273">Ta `@model` dyrektywa umożliwia dostęp do filmu, który kontroler przeszedł do widoku.</span><span class="sxs-lookup"><span data-stu-id="53d96-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="53d96-274">Obiekt `Model` jest silnie wpisywany.</span><span class="sxs-lookup"><span data-stu-id="53d96-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="53d96-275">Na przykład w widoku *Details.cshtml* kod przekazuje każde `DisplayNameFor` pole `DisplayFor` filmu do pomocników `Model` html z silnie wpisanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="53d96-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="53d96-276">I `Create` `Edit` metody i widoki `Movie` również przekazać obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="53d96-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="53d96-277">Sprawdź *Index.cshtml* widoku `Index` i metody w kontrolerze filmy.</span><span class="sxs-lookup"><span data-stu-id="53d96-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="53d96-278">Zwróć uwagę, jak `List` kod tworzy `View` obiekt, gdy wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="53d96-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="53d96-279">Kod przekazuje `Movies` tę listę `Index` z metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="53d96-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="53d96-280">Podczas tworzenia kontrolera filmów, rusztowania `@model` zawierały następującą instrukcję w górnej części pliku *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="53d96-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="53d96-281">Dyrektywa `@model` umożliwia dostęp do listy filmów, które kontroler przeszedł do `Model` widoku przy użyciu obiektu, który jest silnie wpisane.</span><span class="sxs-lookup"><span data-stu-id="53d96-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="53d96-282">Na przykład w widoku *Index.cshtml* kod pętli przez filmy `foreach` z instrukcją `Model` nad silnie wpisany obiektu:</span><span class="sxs-lookup"><span data-stu-id="53d96-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="53d96-283">Ponieważ `Model` obiekt jest silnie wpisany `IEnumerable<Movie>` (jako obiekt), każdy element `Movie`w pętli jest wpisywany jako .</span><span class="sxs-lookup"><span data-stu-id="53d96-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="53d96-284">Wśród innych korzyści, oznacza to, że otrzymasz skompilować czas sprawdzania kodu.</span><span class="sxs-lookup"><span data-stu-id="53d96-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53d96-285">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="53d96-285">Additional resources</span></span>

* [<span data-ttu-id="53d96-286">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="53d96-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="53d96-287">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="53d96-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="53d96-288">[Poprzednia dodanie widoku](adding-view.md)
> [następnej pracy z sql](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="53d96-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="53d96-289">Dodawanie klasy modelu danych</span><span class="sxs-lookup"><span data-stu-id="53d96-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-290">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-291">Kliknij prawym przyciskiem myszy folder *Modele* > **Dodaj** > **klasę**.</span><span class="sxs-lookup"><span data-stu-id="53d96-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="53d96-292">Nazwij klasę **Movie**.</span><span class="sxs-lookup"><span data-stu-id="53d96-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-293">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="53d96-294">Dodaj klasę do folderu *Modele* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="53d96-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="53d96-295">Rusztowanie modelu filmu</span><span class="sxs-lookup"><span data-stu-id="53d96-295">Scaffold the movie model</span></span>

<span data-ttu-id="53d96-296">W tej sekcji model filmu jest szkieletu.</span><span class="sxs-lookup"><span data-stu-id="53d96-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="53d96-297">Oznacza to, że narzędzie rusztowania tworzy strony dla operacji Tworzenia, Odczytu, Aktualizacji i Usuwania (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="53d96-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-298">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-299">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder *Kontrolery* **> Dodaj > nowy element szkieletu**.</span><span class="sxs-lookup"><span data-stu-id="53d96-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![widok powyższego kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="53d96-301">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **Kontroler MVC z widokami, używając programu Entity Framework > Add**.</span><span class="sxs-lookup"><span data-stu-id="53d96-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="53d96-303">Wykonaj okno dialogowe **Dodawanie kontrolera:**</span><span class="sxs-lookup"><span data-stu-id="53d96-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="53d96-304">**Klasa modelu:** *Film (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="53d96-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="53d96-305">**Klasa kontekstu danych:** Wybierz **+** ikonę i dodaj domyślny **MvcMovie.Models.MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="53d96-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Dodawanie kontekstu danych](adding-model/_static/dc.png)

* <span data-ttu-id="53d96-307">**Widoki:** Sprawdzanie wartości domyślnej każdej opcji</span><span class="sxs-lookup"><span data-stu-id="53d96-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="53d96-308">**Nazwa kontrolera:** Zachowaj domyślny *kontroler MoviesController*</span><span class="sxs-lookup"><span data-stu-id="53d96-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="53d96-309">Wybierz **pozycję Dodaj**</span><span class="sxs-lookup"><span data-stu-id="53d96-309">Select **Add**</span></span>

![Okno dialogowe Dodawanie kontrolera](adding-model/_static/add_controller2.png)

<span data-ttu-id="53d96-311">Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="53d96-311">Visual Studio creates:</span></span>

* <span data-ttu-id="53d96-312">[Klasa kontekstu bazy danych](xref:data/ef-mvc/intro#create-the-database-context) core programu Entity Framework *(Data/MvcMovieContext.cs)*</span><span class="sxs-lookup"><span data-stu-id="53d96-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="53d96-313">Kontroler filmów (*Kontrolery/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="53d96-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="53d96-314">Pliki widoku razor dla stron Tworzenie, Usuwanie, Szczegóły, Edycja i Indeks *(Widoki/Filmy/\*.cshtml)*</span><span class="sxs-lookup"><span data-stu-id="53d96-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="53d96-315">Automatyczne tworzenie kontekstu bazy danych i [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (tworzenie, odczytywanie, aktualizowanie i usuwanie) metody działania i widoki jest znany jako *rusztowania*.</span><span class="sxs-lookup"><span data-stu-id="53d96-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="53d96-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53d96-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="53d96-317">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="53d96-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="53d96-318">Zainstaluj narzędzie rusztowania:</span><span class="sxs-lookup"><span data-stu-id="53d96-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="53d96-319">W systemie Linux wyeksportuj ścieżkę narzędzia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="53d96-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="53d96-320">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="53d96-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="53d96-321">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53d96-322">Otwórz okno polecenia w katalogu projektu (katalog zawierający *pliki Program.cs*, *Startup.cs*i *csproj).*</span><span class="sxs-lookup"><span data-stu-id="53d96-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="53d96-323">Zainstaluj narzędzie rusztowania:</span><span class="sxs-lookup"><span data-stu-id="53d96-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="53d96-324">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="53d96-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="53d96-325">Jeśli uruchomisz aplikację i klikniesz łącze **Mvc Movie,** zostanie wyświetlony błąd podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="53d96-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-326">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-327">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="53d96-328">Należy utworzyć bazę danych i użyć funkcji [migracji rdzenia](xref:data/ef-mvc/migrations) EF, aby to zrobić.</span><span class="sxs-lookup"><span data-stu-id="53d96-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="53d96-329">Migracje umożliwia utworzenie bazy danych, która pasuje do modelu danych i zaktualizować schemat bazy danych po zmianie modelu danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="53d96-330">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="53d96-330">Initial migration</span></span>

<span data-ttu-id="53d96-331">W tej sekcji wykonywane są następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="53d96-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="53d96-332">Dodaj migrację początkową.</span><span class="sxs-lookup"><span data-stu-id="53d96-332">Add an initial migration.</span></span>
* <span data-ttu-id="53d96-333">Zaktualizuj bazę danych za pomocą migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="53d96-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-334">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="53d96-335">Z menu **Narzędzia** wybierz pozycję Konsola Menedżera **pakietów** > Programu NuGet Package **Manager** (PMC).</span><span class="sxs-lookup"><span data-stu-id="53d96-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="53d96-337">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="53d96-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="53d96-338">Polecenie `Add-Migration` generuje kod do tworzenia początkowego schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="53d96-339">Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie.</span><span class="sxs-lookup"><span data-stu-id="53d96-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="53d96-340">Argument `Initial` jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="53d96-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="53d96-341">Można użyć dowolnej nazwy, ale zgodnie z konwencją używana jest nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="53d96-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="53d96-342">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="53d96-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="53d96-343">Polecenie `Update-Database` uruchamia `Up` metodę w pliku *Migrations/{time-stamp}_InitialCreate.cs,* który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-344">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="53d96-345">Polecenie `ef migrations add InitialCreate` generuje kod do tworzenia początkowego schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="53d96-346">Schemat bazy danych jest oparty na `MvcMovieContext` modelu określonym w klasie (w pliku *Data/MvcMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="53d96-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="53d96-347">Argument `InitialCreate` jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="53d96-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="53d96-348">Można użyć dowolnej nazwy, ale zgodnie z konwencją jest zaznaczona nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="53d96-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="53d96-349">Badanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="53d96-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="53d96-350">ASP.NET Core jest zbudowany z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="53d96-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="53d96-351">Usługi (takie jak kontekst ef core db) są rejestrowane w DI podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53d96-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="53d96-352">Składniki, które wymagają tych usług (takich jak Razor Pages) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="53d96-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="53d96-353">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="53d96-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="53d96-354">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53d96-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="53d96-355">Narzędzie rusztowania automatycznie utworzyło kontekst bazy danych i zarejestrowało go w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="53d96-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="53d96-356">Sprawdź następującą `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="53d96-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="53d96-357">Podświetlona linia została dodana przez rusztowanie:</span><span class="sxs-lookup"><span data-stu-id="53d96-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="53d96-358">Współrzędne `MvcMovieContext` funkcji EF Core (Tworzenie, Odczyt, Aktualizacja, `Movie` Usuwanie itp.) dla modelu.</span><span class="sxs-lookup"><span data-stu-id="53d96-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="53d96-359">Kontekst danych`MvcMovieContext`( ) pochodzi z [pliku Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="53d96-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="53d96-360">Kontekst danych określa, które jednostki są uwzględnione w modelu danych:</span><span class="sxs-lookup"><span data-stu-id="53d96-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="53d96-361">Poprzedni kod tworzy [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="53d96-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="53d96-362">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="53d96-363">Jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="53d96-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="53d96-364">Nazwa ciągu połączenia jest przekazywana do kontekstu przez wywołanie metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) obiektu.</span><span class="sxs-lookup"><span data-stu-id="53d96-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="53d96-365">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="53d96-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="53d96-366">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="53d96-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="53d96-367">Utworzono kontekst bazy danych i zarejestrowano go w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="53d96-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="53d96-368">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="53d96-368">Test the app</span></span>

* <span data-ttu-id="53d96-369">Uruchom aplikację i `/Movies` dołącz do adresu URL`http://localhost:port/movies`w przeglądarce ( ).</span><span class="sxs-lookup"><span data-stu-id="53d96-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="53d96-370">Jeśli zostanie otrzymasz wyjątek bazy danych podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="53d96-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="53d96-371">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="53d96-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="53d96-372">Przetestuj łącze **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="53d96-372">Test the **Create** link.</span></span> <span data-ttu-id="53d96-373">Wprowadzanie i przesyłanie danych.</span><span class="sxs-lookup"><span data-stu-id="53d96-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="53d96-374">Wprowadzanie przecinków dziesiętnych w polu `Price` może być niemożna wprowadzić przecinków dziesiętnych.</span><span class="sxs-lookup"><span data-stu-id="53d96-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="53d96-375">Aby [obsługiwać sprawdzanie poprawności jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielskie, które używają przecinka (",") dla przecinka dziesiętnego i dla formatów daty nieanglojęzycznych, aplikacja musi być zglobalizowana.</span><span class="sxs-lookup"><span data-stu-id="53d96-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="53d96-376">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="53d96-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="53d96-377">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="53d96-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="53d96-378">Sprawdź `Startup` klasę:</span><span class="sxs-lookup"><span data-stu-id="53d96-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="53d96-379">Powyższy wyróżniony kod pokazuje kontekst bazy danych filmu dodawany do [kontenera iniekcji zależności:](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="53d96-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="53d96-380">`services.AddDbContext<MvcMovieContext>(options =>`określa bazę danych do użycia i parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="53d96-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="53d96-381">`=>`jest [operatorem lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="53d96-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="53d96-382">Otwórz plik *Controllers/MoviesController.cs* i sprawdź konstruktor:</span><span class="sxs-lookup"><span data-stu-id="53d96-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="53d96-383">Konstruktor używa [iniekcji zależności,](xref:fundamentals/dependency-injection) `MvcMovieContext`aby wstrzyknąć kontekst bazy danych ( ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="53d96-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="53d96-384">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="53d96-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="53d96-385">Silnie wpisane modele @model i słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="53d96-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="53d96-386">Wcześniej w tym samouczku, widziałeś, jak kontroler może `ViewData` przekazać dane lub obiekty do widoku przy użyciu słownika.</span><span class="sxs-lookup"><span data-stu-id="53d96-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="53d96-387">Słownik `ViewData` jest obiektem dynamicznym, który zapewnia wygodny późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="53d96-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="53d96-388">MVC zapewnia również możliwość przekazywania silnie wpisane obiekty modelu do widoku.</span><span class="sxs-lookup"><span data-stu-id="53d96-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="53d96-389">To silnie typizowane podejście umożliwia lepsze sprawdzanie czasu kompilacji kodu.</span><span class="sxs-lookup"><span data-stu-id="53d96-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="53d96-390">Mechanizm szkieletu używane to podejście (oznacza to, przekazując model `MoviesController` silnie typizowane) z klasy i widoków podczas tworzenia metod i widoków.</span><span class="sxs-lookup"><span data-stu-id="53d96-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="53d96-391">Sprawdź wygenerowaną `Details` metodę w pliku *Controllers/MoviesController.cs:*</span><span class="sxs-lookup"><span data-stu-id="53d96-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="53d96-392">Parametr `id` jest zazwyczaj przekazywany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="53d96-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="53d96-393">Na `https://localhost:5001/movies/details/1` przykład zestawy:</span><span class="sxs-lookup"><span data-stu-id="53d96-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="53d96-394">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="53d96-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="53d96-395">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="53d96-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="53d96-396">Identyfikator do 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="53d96-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="53d96-397">Można również przekazać `id` w ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="53d96-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="53d96-398">Parametr `id` jest zdefiniowany jako typ`int?` [nullable](/dotnet/csharp/programming-guide/nullable-types/index) ( ) w przypadku, gdy wartość identyfikatora nie jest podana.</span><span class="sxs-lookup"><span data-stu-id="53d96-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="53d96-399">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przekazywane `FirstOrDefaultAsync` do wybierania jednostek filmu, które pasują do danych trasy lub wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="53d96-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="53d96-400">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest `Details` przekazywane do widoku:</span><span class="sxs-lookup"><span data-stu-id="53d96-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="53d96-401">Sprawdź zawartość pliku *Views/Movies/Details.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="53d96-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="53d96-402">Dołączając `@model` instrukcję w górnej części pliku widoku, można określić typ obiektu, którego oczekuje widok.</span><span class="sxs-lookup"><span data-stu-id="53d96-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="53d96-403">Podczas tworzenia kontrolera filmów `@model` następująca instrukcja została automatycznie uwzględniona w górnej części pliku *Details.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="53d96-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="53d96-404">Ta `@model` dyrektywa umożliwia dostęp do filmu, który kontroler przeszedł `Model` do widoku przy użyciu obiektu, który jest silnie wpisane.</span><span class="sxs-lookup"><span data-stu-id="53d96-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="53d96-405">Na przykład w widoku *Details.cshtml* kod przekazuje każde `DisplayNameFor` pole `DisplayFor` filmu do pomocników `Model` html z silnie wpisanym obiektem.</span><span class="sxs-lookup"><span data-stu-id="53d96-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="53d96-406">I `Create` `Edit` metody i widoki `Movie` również przekazać obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="53d96-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="53d96-407">Sprawdź *Index.cshtml* widoku `Index` i metody w kontrolerze filmy.</span><span class="sxs-lookup"><span data-stu-id="53d96-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="53d96-408">Zwróć uwagę, jak `List` kod tworzy `View` obiekt, gdy wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="53d96-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="53d96-409">Kod przekazuje `Movies` tę listę `Index` z metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="53d96-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="53d96-410">Podczas tworzenia kontrolera filmów, rusztowania automatycznie `@model` zawarte następujące instrukcje w górnej części pliku *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="53d96-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="53d96-411">Dyrektywa `@model` umożliwia dostęp do listy filmów, które kontroler przeszedł do `Model` widoku przy użyciu obiektu, który jest silnie wpisane.</span><span class="sxs-lookup"><span data-stu-id="53d96-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="53d96-412">Na przykład w widoku *Index.cshtml* kod pętli przez filmy `foreach` z instrukcją `Model` nad silnie wpisany obiektu:</span><span class="sxs-lookup"><span data-stu-id="53d96-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="53d96-413">Ponieważ `Model` obiekt jest silnie wpisany `IEnumerable<Movie>` (jako obiekt), każdy element `Movie`w pętli jest wpisywany jako .</span><span class="sxs-lookup"><span data-stu-id="53d96-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="53d96-414">Wśród innych korzyści, oznacza to, że można uzyskać skompilować czas sprawdzania kodu:</span><span class="sxs-lookup"><span data-stu-id="53d96-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53d96-415">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="53d96-415">Additional resources</span></span>

* [<span data-ttu-id="53d96-416">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="53d96-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="53d96-417">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="53d96-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="53d96-418">[Poprzednia Dodawanie widoku](adding-view.md)
> [następnej pracy z bazą danych](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="53d96-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
