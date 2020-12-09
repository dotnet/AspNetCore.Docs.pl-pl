---
title: Część 4. Dodawanie modelu do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 4 samouczków z serii ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: d99d5bfd84c8c7e7d0c479964211591be434330c
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855524"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="96e37-103">Część 4. Dodawanie modelu do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="96e37-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="96e37-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="96e37-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="96e37-105">W tej sekcji dodasz klasy do zarządzania filmami w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="96e37-106">Te klasy będą częścią "**m** odelu" aplikacji **m** VC.</span><span class="sxs-lookup"><span data-stu-id="96e37-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="96e37-107">Te klasy są używane z [Entity Framework Core](/ef/core) (Ef Core) do pracy z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="96e37-108">EF Core to struktura obiektu mapowania relacyjnego (ORM), która upraszcza kod dostępu do danych, który trzeba napisać.</span><span class="sxs-lookup"><span data-stu-id="96e37-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="96e37-109">Klasy modelu, które tworzysz, są nazywane klasami POCO ( **z Lain** **P** LR **C** **o** biekty), ponieważ nie mają żadnej zależności od EF Core.</span><span class="sxs-lookup"><span data-stu-id="96e37-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="96e37-110">Po prostu definiują właściwości danych, które będą przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="96e37-111">W tym samouczku najpierw napiszesz klasy modelu, a EF Core tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="96e37-112">Dodaj klasę modelu danych</span><span class="sxs-lookup"><span data-stu-id="96e37-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-114">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="96e37-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="96e37-115">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="96e37-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="96e37-117">Dodaj plik o nazwie *Movie.cs* do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="96e37-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="96e37-119">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **nową klasę**  >  **pustą** klasy.</span><span class="sxs-lookup"><span data-stu-id="96e37-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="96e37-120">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="96e37-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="96e37-121">Zaktualizuj plik *Movie.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="96e37-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="96e37-122">`Movie`Klasa zawiera `Id` pole, które jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="96e37-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="96e37-123"><xref:System.ComponentModel.DataAnnotations.DataType>Atrybut on `ReleaseDate` określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="96e37-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="96e37-124">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="96e37-124">With this attribute:</span></span>

* <span data-ttu-id="96e37-125">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="96e37-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="96e37-126">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="96e37-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="96e37-127">[Adnotacje DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="96e37-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="96e37-128">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="96e37-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-130">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="96e37-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="96e37-132">W obszarze PMC Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="96e37-133">Poprzednie polecenie dodaje dostawcę SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="96e37-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="96e37-134">Pakiet dostawcy instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="96e37-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="96e37-135">Dodatkowe pakiety są instalowane automatycznie w kroku tworzenia szkieletu w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="96e37-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-137">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="96e37-138">W menu **projekt** wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="96e37-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="96e37-139">W polu **wyszukiwania** w prawym górnym rogu wprowadź `Microsoft.EntityFrameworkCore.SQLite` i naciśnij klawisz **Return** , aby wyszukać.</span><span class="sxs-lookup"><span data-stu-id="96e37-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="96e37-140">Wybierz pasujący pakiet NuGet i naciśnij przycisk **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="96e37-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Dodaj Entity Framework Core pakiet NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="96e37-142">Zostanie wyświetlone okno dialogowe **Wybieranie projektów** z `MvcMovie` wybranym projektem.</span><span class="sxs-lookup"><span data-stu-id="96e37-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="96e37-143">Naciśnij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="96e37-143">Press the **Ok** button.</span></span>

<span data-ttu-id="96e37-144">Zostanie wyświetlone okno dialogowe **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="96e37-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="96e37-145">Przejrzyj odpowiednie licencje, a następnie kliknij przycisk **Akceptuj** .</span><span class="sxs-lookup"><span data-stu-id="96e37-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="96e37-146">Powtórz powyższe kroki, aby zainstalować następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="96e37-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="96e37-147">Uruchom następujące polecenie interfejsu wiersza polecenia platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="96e37-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="96e37-148">Poprzednie polecenie dodaje narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="96e37-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="96e37-149">Tworzenie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="96e37-149">Create a database context class</span></span>

<span data-ttu-id="96e37-150">Klasa kontekstu bazy danych jest wymagana do koordynowania funkcji EF Core (tworzenie, odczytywanie, aktualizowanie, usuwanie) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="96e37-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="96e37-151">Kontekst bazy danych pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i określa jednostki, które mają zostać uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="96e37-152">Utwórz folder *danych* .</span><span class="sxs-lookup"><span data-stu-id="96e37-152">Create a *Data* folder.</span></span>

<span data-ttu-id="96e37-153">Dodaj plik *Data/MvcMovieContext. cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="96e37-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="96e37-154">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="96e37-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="96e37-155">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="96e37-156">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="96e37-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="96e37-157">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="96e37-157">Register the database context</span></span>

<span data-ttu-id="96e37-158">ASP.NET Core jest skompilowany przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="96e37-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="96e37-159">Usługi (takie jak kontekst EF Core DB) muszą być zarejestrowane przy użyciu funkcji "DI" podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96e37-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="96e37-160">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="96e37-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="96e37-161">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="96e37-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="96e37-162">W tej sekcji rejestrujesz kontekst bazy danych przy użyciu funkcji DI Container.</span><span class="sxs-lookup"><span data-stu-id="96e37-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="96e37-163">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e37-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="96e37-164">Dodaj następujący wyróżniony kod w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="96e37-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-165">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-166">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="96e37-167">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="96e37-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="96e37-168">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="96e37-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="96e37-169">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="96e37-169">Add a database connection string</span></span>

<span data-ttu-id="96e37-170">Dodaj parametry połączenia do *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="96e37-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-171">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-172">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="96e37-173">Kompiluj projekt jako sprawdzenie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="96e37-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="96e37-174">Strony z filmem szkieletu</span><span class="sxs-lookup"><span data-stu-id="96e37-174">Scaffold movie pages</span></span>

<span data-ttu-id="96e37-175">Użyj narzędzia do tworzenia szkieletu, aby utworzyć strony z przykładem tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="96e37-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-176">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-177">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *controllers* , **> Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="96e37-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Widok powyżej kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="96e37-179">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC z widokami przy użyciu Entity Framework > Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="96e37-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="96e37-181">Ukończ okno dialogowe **Dodawanie kontrolera** :</span><span class="sxs-lookup"><span data-stu-id="96e37-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="96e37-182">**Model Class:** *Movie (MvcMovie. models)*</span><span class="sxs-lookup"><span data-stu-id="96e37-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="96e37-183">**Klasa kontekstu danych:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="96e37-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Dodaj kontekst danych](adding-model/_static/dc5.png)

* <span data-ttu-id="96e37-185">**Widoki:** Zachowaj wartość domyślną dla każdej zaznaczonej opcji</span><span class="sxs-lookup"><span data-stu-id="96e37-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="96e37-186">**Nazwa kontrolera:** Zachowaj domyślną *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="96e37-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="96e37-187">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="96e37-187">Select **Add**</span></span>

<span data-ttu-id="96e37-188">Program Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="96e37-188">Visual Studio creates:</span></span>

* <span data-ttu-id="96e37-189">Kontroler filmów (*controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="96e37-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="96e37-190">Razor Wyświetlanie plików na potrzeby tworzenia, usuwania, szczegółów, edytowania i indeksowania stron (*widoki/filmy/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="96e37-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="96e37-191">Automatyczne tworzenie tych plików jest znane jako *rusztowania*.</span><span class="sxs-lookup"><span data-stu-id="96e37-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="96e37-193">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="96e37-194">W systemie Linux wyeksportuj ścieżkę narzędzia do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="96e37-195">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-196">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="96e37-197">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="96e37-198">Wyeksportuj ścieżkę narzędzia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="96e37-199">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="96e37-200">Nie można jeszcze użyć stron szkieletowych, ponieważ baza danych nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="96e37-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="96e37-201">Jeśli uruchomisz aplikację i klikniesz link **aplikacji filmowej** , uzyskasz dostęp do *otwartej bazy danych* lub *nie ma takiej tabeli:* komunikat o błędzie filmu.</span><span class="sxs-lookup"><span data-stu-id="96e37-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="96e37-202">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="96e37-202">Initial migration</span></span>

<span data-ttu-id="96e37-203">Użyj funkcji [migracji](xref:data/ef-mvc/migrations) EF Core, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="96e37-204">Migracje to zestaw narzędzi umożliwiających tworzenie i aktualizowanie bazy danych w celu dopasowania jej do modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-205">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-206">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="96e37-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="96e37-207">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96e37-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="96e37-208">`Add-Migration InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="96e37-209">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="96e37-210">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="96e37-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="96e37-211">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="96e37-212">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie.</span><span class="sxs-lookup"><span data-stu-id="96e37-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="96e37-213">`Update-Database`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="96e37-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="96e37-214">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="96e37-215">Polecenie aktualizacji bazy danych generuje następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="96e37-216">Nie określono typu dla kolumny dziesiętnej "price" w typie jednostki "Movie".</span><span class="sxs-lookup"><span data-stu-id="96e37-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="96e37-217">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="96e37-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="96e37-218">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="96e37-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="96e37-219">Możesz zignorować to ostrzeżenie. zostanie on rozwiązany w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="96e37-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-220">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="96e37-221">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="96e37-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="96e37-222">`ef migrations add InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="96e37-223">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="96e37-224">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="96e37-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="96e37-225">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="96e37-226">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie (w pliku *Data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="96e37-227">`ef database update`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="96e37-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="96e37-228">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="96e37-229">Klasa InitialCreate</span><span class="sxs-lookup"><span data-stu-id="96e37-229">The InitialCreate class</span></span>

<span data-ttu-id="96e37-230">Przejrzyj *migracje/{timestamp} _InitialCreate* pliku migracji CS:</span><span class="sxs-lookup"><span data-stu-id="96e37-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="96e37-231">`Up`Metoda tworzy tabelę filmów i konfiguruje `Id` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="96e37-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="96e37-232">`Down`Metoda przywraca zmiany schematu wykonane podczas `Up` migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="96e37-233">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="96e37-233">Test the app</span></span>

* <span data-ttu-id="96e37-234">Uruchom aplikację i kliknij link **aplikacji filmowej** .</span><span class="sxs-lookup"><span data-stu-id="96e37-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="96e37-235">Jeśli zostanie wyświetlony wyjątek podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="96e37-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-236">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-237">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="96e37-238">Prawdopodobnie pominięto [krok migracji](#migration).</span><span class="sxs-lookup"><span data-stu-id="96e37-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="96e37-239">Przetestuj stronę **Tworzenie** .</span><span class="sxs-lookup"><span data-stu-id="96e37-239">Test the **Create** page.</span></span> <span data-ttu-id="96e37-240">Wprowadź i prześlij dane.</span><span class="sxs-lookup"><span data-stu-id="96e37-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="96e37-241">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="96e37-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="96e37-242">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="96e37-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="96e37-243">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="96e37-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="96e37-244">Przetestuj strony **Edytuj**, **szczegóły** i **Usuń** .</span><span class="sxs-lookup"><span data-stu-id="96e37-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="96e37-245">Wstrzykiwanie zależności w kontrolerze</span><span class="sxs-lookup"><span data-stu-id="96e37-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-246">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-247">Otwórz plik *controllers/MoviesController. cs* i zapoznaj się z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="96e37-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="96e37-248">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="96e37-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="96e37-249">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="96e37-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-250">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="96e37-251">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="96e37-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="96e37-252">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="96e37-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="96e37-253">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="96e37-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="96e37-254">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="96e37-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="96e37-255">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="96e37-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="96e37-256">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="96e37-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="96e37-257">Modele silnie wpisane i @model słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="96e37-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="96e37-258">Wcześniej w tym samouczku pokazano, jak kontroler może przekazać dane lub obiekty do widoku przy użyciu `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="96e37-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="96e37-259">`ViewData`Słownik jest obiektem dynamicznym, który zapewnia wygodny, późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="96e37-260">MVC oferuje również możliwość przekazywania obiektów modelu silnie typu do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="96e37-261">Takie silnie wpisane podejście umożliwia sprawdzanie kodu czasu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="96e37-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="96e37-262">Mechanizm tworzenia szkieletu używa tego podejścia (czyli przekazania silnie określonego modelu) z `MoviesController` klasą i widokami.</span><span class="sxs-lookup"><span data-stu-id="96e37-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="96e37-263">Przejrzyj wygenerowaną `Details` metodę w pliku *controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="96e37-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="96e37-264">`id`Parametr jest zazwyczaj przesyłany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="96e37-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="96e37-265">Na przykład `https://localhost:5001/movies/details/1` zestawy:</span><span class="sxs-lookup"><span data-stu-id="96e37-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="96e37-266">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="96e37-267">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="96e37-268">Identyfikator 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="96e37-269">Można również przekazać `id` za pomocą ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="96e37-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="96e37-270">`id`Parametr jest zdefiniowany jako [typ dopuszczający wartość null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) w przypadku, gdy nie podano wartości identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="96e37-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="96e37-271">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przesyłane do, `FirstOrDefaultAsync` Aby wybrać jednostki filmu, które pasują do wartości danych trasy lub ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="96e37-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="96e37-272">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest przesyłane do `Details` widoku:</span><span class="sxs-lookup"><span data-stu-id="96e37-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="96e37-273">Zapoznaj się z zawartością pliku *widoki/filmy/szczegóły. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="96e37-274">`@model`Instrukcja w górnej części pliku widoku określa typ obiektu, który jest oczekiwany przez widok.</span><span class="sxs-lookup"><span data-stu-id="96e37-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="96e37-275">Po utworzeniu kontrolera filmu `@model` uwzględniono następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="96e37-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="96e37-276">Ta `@model` dyrektywa zezwala na dostęp do filmu, który kontroler przeszedł do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="96e37-277">`Model`Obiekt ma silną wartość.</span><span class="sxs-lookup"><span data-stu-id="96e37-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="96e37-278">Na przykład w widoku *details. cshtml* kod przekazuje każde pole filmu do `DisplayNameFor` `DisplayFor` pomocników HTML z obiektem o jednoznacznie określonym typie `Model` .</span><span class="sxs-lookup"><span data-stu-id="96e37-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="96e37-279">`Create`Metody i `Edit` i widoki również przekazują `Movie` obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="96e37-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="96e37-280">Sprawdź widok *index. cshtml* i `Index` metodę w kontrolerze filmów.</span><span class="sxs-lookup"><span data-stu-id="96e37-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="96e37-281">Zwróć uwagę, jak kod tworzy `List` obiekt, gdy wywołuje `View` metodę.</span><span class="sxs-lookup"><span data-stu-id="96e37-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="96e37-282">Kod przekazuje tę `Movies` listę z `Index` metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="96e37-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="96e37-283">Gdy kontroler filmów został utworzony, szkielet zawiera następujące `@model` instrukcje w górnej części pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="96e37-284">`@model`Dyrektywa pozwala uzyskać dostęp do listy filmów przekazaną przez kontroler do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="96e37-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="96e37-285">Na przykład w widoku *index. cshtml* kod przechodzi przez filmy z `foreach` instrukcją względem obiektu silnie wpisanego `Model` :</span><span class="sxs-lookup"><span data-stu-id="96e37-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="96e37-286">Ponieważ `Model` obiekt jest silnie określony (jako `IEnumerable<Movie>` obiekt), każdy element w pętli jest wpisywany jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="96e37-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="96e37-287">Dzięki temu możesz uzyskać kontrolę czasu kompilowania kodu.</span><span class="sxs-lookup"><span data-stu-id="96e37-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96e37-288">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96e37-288">Additional resources</span></span>

* [<span data-ttu-id="96e37-289">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="96e37-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="96e37-290">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="96e37-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="96e37-291">[Poprzednie dodanie widoku](adding-view.md) 
>  [Następna praca z SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="96e37-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="96e37-292">Dodaj klasę modelu danych</span><span class="sxs-lookup"><span data-stu-id="96e37-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-293">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-294">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="96e37-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="96e37-295">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="96e37-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="96e37-297">Dodaj plik o nazwie *Movie.cs* do folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="96e37-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-298">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="96e37-299">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **nową klasę**  >  **pustą** klasy.</span><span class="sxs-lookup"><span data-stu-id="96e37-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="96e37-300">Nazwij plik *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="96e37-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="96e37-301">Zaktualizuj plik *Movie.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="96e37-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="96e37-302">`Movie`Klasa zawiera `Id` pole, które jest wymagane przez bazę danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="96e37-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="96e37-303"><xref:System.ComponentModel.DataAnnotations.DataType>Atrybut on `ReleaseDate` określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="96e37-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="96e37-304">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="96e37-304">With this attribute:</span></span>

* <span data-ttu-id="96e37-305">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="96e37-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="96e37-306">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="96e37-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="96e37-307">[Adnotacje DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="96e37-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="96e37-308">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="96e37-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-309">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-310">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="96e37-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="96e37-312">W obszarze PMC Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="96e37-313">Poprzednie polecenie dodaje dostawcę SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="96e37-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="96e37-314">Pakiet dostawcy instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="96e37-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="96e37-315">Dodatkowe pakiety są instalowane automatycznie w kroku tworzenia szkieletu w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="96e37-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-317">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="96e37-318">W menu **projekt** wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="96e37-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="96e37-319">W polu **wyszukiwania** w prawym górnym rogu wprowadź `Microsoft.EntityFrameworkCore.SQLite` i naciśnij klawisz **Return** , aby wyszukać.</span><span class="sxs-lookup"><span data-stu-id="96e37-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="96e37-320">Wybierz pasujący pakiet NuGet i naciśnij przycisk **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="96e37-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Dodaj Entity Framework Core pakiet NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="96e37-322">Zostanie wyświetlone okno dialogowe **Wybieranie projektów** z `MvcMovie` wybranym projektem.</span><span class="sxs-lookup"><span data-stu-id="96e37-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="96e37-323">Naciśnij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="96e37-323">Press the **Ok** button.</span></span>

<span data-ttu-id="96e37-324">Zostanie wyświetlone okno dialogowe **akceptacji licencji** .</span><span class="sxs-lookup"><span data-stu-id="96e37-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="96e37-325">Przejrzyj odpowiednie licencje, a następnie kliknij przycisk **Akceptuj** .</span><span class="sxs-lookup"><span data-stu-id="96e37-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="96e37-326">Powtórz powyższe kroki, aby zainstalować następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="96e37-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="96e37-327">Tworzenie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="96e37-327">Create a database context class</span></span>

<span data-ttu-id="96e37-328">Klasa kontekstu bazy danych jest wymagana do koordynowania funkcji EF Core (tworzenie, odczytywanie, aktualizowanie, usuwanie) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="96e37-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="96e37-329">Kontekst bazy danych pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) i określa jednostki, które mają zostać uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="96e37-330">Utwórz folder *danych* .</span><span class="sxs-lookup"><span data-stu-id="96e37-330">Create a *Data* folder.</span></span>

<span data-ttu-id="96e37-331">Dodaj plik *Data/MvcMovieContext. cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="96e37-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="96e37-332">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="96e37-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="96e37-333">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="96e37-334">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="96e37-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="96e37-335">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="96e37-335">Register the database context</span></span>

<span data-ttu-id="96e37-336">ASP.NET Core jest skompilowany przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="96e37-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="96e37-337">Usługi (takie jak kontekst EF Core DB) muszą być zarejestrowane przy użyciu funkcji "DI" podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96e37-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="96e37-338">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="96e37-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="96e37-339">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="96e37-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="96e37-340">W tej sekcji rejestrujesz kontekst bazy danych przy użyciu funkcji DI Container.</span><span class="sxs-lookup"><span data-stu-id="96e37-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="96e37-341">Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="96e37-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="96e37-342">Dodaj następujący wyróżniony kod w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="96e37-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-343">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-344">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="96e37-345">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="96e37-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="96e37-346">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="96e37-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="96e37-347">Dodaj parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="96e37-347">Add a database connection string</span></span>

<span data-ttu-id="96e37-348">Dodaj parametry połączenia do *appsettings.json* pliku:</span><span class="sxs-lookup"><span data-stu-id="96e37-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-349">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-350">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="96e37-351">Kompiluj projekt jako sprawdzenie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="96e37-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="96e37-352">Strony z filmem szkieletu</span><span class="sxs-lookup"><span data-stu-id="96e37-352">Scaffold movie pages</span></span>

<span data-ttu-id="96e37-353">Użyj narzędzia do tworzenia szkieletu, aby utworzyć strony z przykładem tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="96e37-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-354">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-355">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *controllers* , **> Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="96e37-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Widok powyżej kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="96e37-357">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC z widokami przy użyciu Entity Framework > Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="96e37-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="96e37-359">Ukończ okno dialogowe **Dodawanie kontrolera** :</span><span class="sxs-lookup"><span data-stu-id="96e37-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="96e37-360">**Model Class:** *Movie (MvcMovie. models)*</span><span class="sxs-lookup"><span data-stu-id="96e37-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="96e37-361">**Klasa kontekstu danych:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="96e37-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Dodaj kontekst danych](adding-model/_static/dc3.png)

* <span data-ttu-id="96e37-363">**Widoki:** Zachowaj wartość domyślną dla każdej zaznaczonej opcji</span><span class="sxs-lookup"><span data-stu-id="96e37-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="96e37-364">**Nazwa kontrolera:** Zachowaj domyślną *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="96e37-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="96e37-365">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="96e37-365">Select **Add**</span></span>

<span data-ttu-id="96e37-366">Program Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="96e37-366">Visual Studio creates:</span></span>

* <span data-ttu-id="96e37-367">Kontroler filmów (*controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="96e37-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="96e37-368">Razor Wyświetlanie plików na potrzeby tworzenia, usuwania, szczegółów, edytowania i indeksowania stron (*widoki/filmy/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="96e37-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="96e37-369">Automatyczne tworzenie tych plików jest znane jako *rusztowania*.</span><span class="sxs-lookup"><span data-stu-id="96e37-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="96e37-371">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="96e37-372">W systemie Linux wyeksportuj ścieżkę narzędzia do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="96e37-373">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-374">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="96e37-375">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="96e37-376">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="96e37-377">Nie można jeszcze użyć stron szkieletowych, ponieważ baza danych nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="96e37-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="96e37-378">Jeśli uruchomisz aplikację i klikniesz link **aplikacji filmowej** , uzyskasz dostęp do *otwartej bazy danych* lub *nie ma takiej tabeli:* komunikat o błędzie filmu.</span><span class="sxs-lookup"><span data-stu-id="96e37-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="96e37-379">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="96e37-379">Initial migration</span></span>

<span data-ttu-id="96e37-380">Użyj funkcji [migracji](xref:data/ef-mvc/migrations) EF Core, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="96e37-381">Migracje to zestaw narzędzi umożliwiających tworzenie i aktualizowanie bazy danych w celu dopasowania jej do modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-382">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-383">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="96e37-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="96e37-384">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96e37-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="96e37-385">`Add-Migration InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="96e37-386">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="96e37-387">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="96e37-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="96e37-388">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="96e37-389">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie.</span><span class="sxs-lookup"><span data-stu-id="96e37-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="96e37-390">`Update-Database`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="96e37-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="96e37-391">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="96e37-392">Polecenie aktualizacji bazy danych generuje następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="96e37-393">Nie określono typu dla kolumny dziesiętnej "price" w typie jednostki "Movie".</span><span class="sxs-lookup"><span data-stu-id="96e37-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="96e37-394">Spowoduje to, że wartości powinny być obcinane w trybie dyskretnym, jeśli nie mieszczą się w domyślnej precyzji i skali.</span><span class="sxs-lookup"><span data-stu-id="96e37-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="96e37-395">Jawnie określ typ kolumny programu SQL Server, który może pomieścić wszystkie wartości przy użyciu "HasColumnType ()".</span><span class="sxs-lookup"><span data-stu-id="96e37-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="96e37-396">Możesz zignorować to ostrzeżenie. zostanie on rozwiązany w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="96e37-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-397">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="96e37-398">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="96e37-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="96e37-399">`ef migrations add InitialCreate`: Generuje *migrację/{timestamp} _InitialCreate. cs* pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="96e37-400">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="96e37-401">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="96e37-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="96e37-402">Ponieważ jest to pierwsza migracja, wygenerowana Klasa zawiera kod, aby utworzyć schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="96e37-403">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie (w pliku *Data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="96e37-404">`ef database update`: Aktualizuje bazę danych do najnowszej migracji, która została utworzona przez poprzednie polecenie.</span><span class="sxs-lookup"><span data-stu-id="96e37-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="96e37-405">To polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="96e37-406">Klasa InitialCreate</span><span class="sxs-lookup"><span data-stu-id="96e37-406">The InitialCreate class</span></span>

<span data-ttu-id="96e37-407">Przejrzyj *migracje/{timestamp} _InitialCreate* pliku migracji CS:</span><span class="sxs-lookup"><span data-stu-id="96e37-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="96e37-408">`Up`Metoda tworzy tabelę filmów i konfiguruje `Id` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="96e37-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="96e37-409">`Down`Metoda przywraca zmiany schematu wykonane podczas `Up` migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="96e37-410">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="96e37-410">Test the app</span></span>

* <span data-ttu-id="96e37-411">Uruchom aplikację i kliknij link **aplikacji filmowej** .</span><span class="sxs-lookup"><span data-stu-id="96e37-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="96e37-412">Jeśli zostanie wyświetlony wyjątek podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="96e37-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-413">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-414">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="96e37-415">Prawdopodobnie pominięto [krok migracji](#migration).</span><span class="sxs-lookup"><span data-stu-id="96e37-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="96e37-416">Przetestuj stronę **Tworzenie** .</span><span class="sxs-lookup"><span data-stu-id="96e37-416">Test the **Create** page.</span></span> <span data-ttu-id="96e37-417">Wprowadź i prześlij dane.</span><span class="sxs-lookup"><span data-stu-id="96e37-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="96e37-418">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="96e37-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="96e37-419">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="96e37-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="96e37-420">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="96e37-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="96e37-421">Przetestuj strony **Edytuj**, **szczegóły** i **Usuń** .</span><span class="sxs-lookup"><span data-stu-id="96e37-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="96e37-422">Wstrzykiwanie zależności w kontrolerze</span><span class="sxs-lookup"><span data-stu-id="96e37-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-423">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-424">Otwórz plik *controllers/MoviesController. cs* i zapoznaj się z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="96e37-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="96e37-425">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="96e37-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="96e37-426">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="96e37-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-427">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="96e37-428">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="96e37-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="96e37-429">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="96e37-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="96e37-430">Używanie oprogramowania SQLite do programowania, SQL Server do produkcji</span><span class="sxs-lookup"><span data-stu-id="96e37-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="96e37-431">Po wybraniu oprogramowania SQLite kod wygenerowany przez szablon jest gotowy do programowania.</span><span class="sxs-lookup"><span data-stu-id="96e37-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="96e37-432">Poniższy kod pokazuje, jak wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="96e37-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="96e37-433">`IWebHostEnvironment` jest wstrzykiwane, więc `ConfigureServices` może korzystać z oprogramowania SQLite w programowaniu i SQL Server w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="96e37-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="96e37-434">Modele silnie wpisane i @model słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="96e37-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="96e37-435">Wcześniej w tym samouczku pokazano, jak kontroler może przekazać dane lub obiekty do widoku przy użyciu `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="96e37-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="96e37-436">`ViewData`Słownik jest obiektem dynamicznym, który zapewnia wygodny, późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="96e37-437">MVC oferuje również możliwość przekazywania obiektów modelu silnie typu do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="96e37-438">Takie silnie wpisane podejście umożliwia sprawdzanie kodu czasu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="96e37-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="96e37-439">Mechanizm tworzenia szkieletu używa tego podejścia (czyli przekazania silnie określonego modelu) z `MoviesController` klasą i widokami.</span><span class="sxs-lookup"><span data-stu-id="96e37-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="96e37-440">Przejrzyj wygenerowaną `Details` metodę w pliku *controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="96e37-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="96e37-441">`id`Parametr jest zazwyczaj przesyłany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="96e37-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="96e37-442">Na przykład `https://localhost:5001/movies/details/1` zestawy:</span><span class="sxs-lookup"><span data-stu-id="96e37-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="96e37-443">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="96e37-444">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="96e37-445">Identyfikator 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="96e37-446">Można również przekazać `id` za pomocą ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="96e37-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="96e37-447">`id`Parametr jest zdefiniowany jako [typ dopuszczający wartość null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) w przypadku, gdy nie podano wartości identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="96e37-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="96e37-448">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przesyłane do, `FirstOrDefaultAsync` Aby wybrać jednostki filmu, które pasują do wartości danych trasy lub ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="96e37-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="96e37-449">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest przesyłane do `Details` widoku:</span><span class="sxs-lookup"><span data-stu-id="96e37-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="96e37-450">Zapoznaj się z zawartością pliku *widoki/filmy/szczegóły. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="96e37-451">`@model`Instrukcja w górnej części pliku widoku określa typ obiektu, który jest oczekiwany przez widok.</span><span class="sxs-lookup"><span data-stu-id="96e37-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="96e37-452">Po utworzeniu kontrolera filmu `@model` uwzględniono następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="96e37-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="96e37-453">Ta `@model` dyrektywa zezwala na dostęp do filmu, który kontroler przeszedł do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="96e37-454">`Model`Obiekt ma silną wartość.</span><span class="sxs-lookup"><span data-stu-id="96e37-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="96e37-455">Na przykład w widoku *details. cshtml* kod przekazuje każde pole filmu do `DisplayNameFor` `DisplayFor` pomocników HTML z obiektem o jednoznacznie określonym typie `Model` .</span><span class="sxs-lookup"><span data-stu-id="96e37-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="96e37-456">`Create`Metody i `Edit` i widoki również przekazują `Movie` obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="96e37-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="96e37-457">Sprawdź widok *index. cshtml* i `Index` metodę w kontrolerze filmów.</span><span class="sxs-lookup"><span data-stu-id="96e37-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="96e37-458">Zwróć uwagę, jak kod tworzy `List` obiekt, gdy wywołuje `View` metodę.</span><span class="sxs-lookup"><span data-stu-id="96e37-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="96e37-459">Kod przekazuje tę `Movies` listę z `Index` metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="96e37-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="96e37-460">Gdy kontroler filmów został utworzony, szkielet zawiera następujące `@model` instrukcje w górnej części pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="96e37-461">`@model`Dyrektywa pozwala uzyskać dostęp do listy filmów przekazaną przez kontroler do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="96e37-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="96e37-462">Na przykład w widoku *index. cshtml* kod przechodzi przez filmy z `foreach` instrukcją względem obiektu silnie wpisanego `Model` :</span><span class="sxs-lookup"><span data-stu-id="96e37-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="96e37-463">Ponieważ `Model` obiekt jest silnie określony (jako `IEnumerable<Movie>` obiekt), każdy element w pętli jest wpisywany jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="96e37-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="96e37-464">Dzięki temu możesz uzyskać kontrolę czasu kompilowania kodu.</span><span class="sxs-lookup"><span data-stu-id="96e37-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96e37-465">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96e37-465">Additional resources</span></span>

* [<span data-ttu-id="96e37-466">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="96e37-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="96e37-467">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="96e37-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="96e37-468">[Poprzednie dodanie widoku](adding-view.md) 
>  [Następna praca z SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="96e37-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="96e37-469">Dodaj klasę modelu danych</span><span class="sxs-lookup"><span data-stu-id="96e37-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-470">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-471">Kliknij prawym przyciskiem myszy folder *modele* > **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="96e37-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="96e37-472">Nazwij **film** klasy.</span><span class="sxs-lookup"><span data-stu-id="96e37-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-473">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="96e37-474">Dodaj klasę do folderu *models* o nazwie *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="96e37-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="96e37-475">Tworzenie szkieletu modelu filmu</span><span class="sxs-lookup"><span data-stu-id="96e37-475">Scaffold the movie model</span></span>

<span data-ttu-id="96e37-476">W tej sekcji model filmu jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="96e37-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="96e37-477">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu filmu.</span><span class="sxs-lookup"><span data-stu-id="96e37-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-478">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-479">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *controllers* , **> Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="96e37-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Widok powyżej kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="96e37-481">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC z widokami przy użyciu Entity Framework > Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="96e37-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Okno dialogowe Dodawanie szkieletu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="96e37-483">Ukończ okno dialogowe **Dodawanie kontrolera** :</span><span class="sxs-lookup"><span data-stu-id="96e37-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="96e37-484">**Model Class:** *Movie (MvcMovie. models)*</span><span class="sxs-lookup"><span data-stu-id="96e37-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="96e37-485">**Klasa kontekstu danych:** Wybierz **+** ikonę i Dodaj domyślną **MvcMovie. models. MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="96e37-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Dodaj kontekst danych](adding-model/_static/dc.png)

* <span data-ttu-id="96e37-487">**Widoki:** Zachowaj wartość domyślną dla każdej zaznaczonej opcji</span><span class="sxs-lookup"><span data-stu-id="96e37-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="96e37-488">**Nazwa kontrolera:** Zachowaj domyślną *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="96e37-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="96e37-489">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="96e37-489">Select **Add**</span></span>

![Okno dialogowe Dodawanie kontrolera](adding-model/_static/add_controller2.png)

<span data-ttu-id="96e37-491">Program Visual Studio tworzy:</span><span class="sxs-lookup"><span data-stu-id="96e37-491">Visual Studio creates:</span></span>

* <span data-ttu-id="96e37-492">[Klasa kontekstu bazy danych](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="96e37-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="96e37-493">Kontroler filmów (*controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="96e37-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="96e37-494">Razor Wyświetlanie plików na potrzeby tworzenia, usuwania, szczegółów, edytowania i indeksowania stron (*widoki/filmy/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="96e37-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="96e37-495">Automatyczne tworzenie kontekstu bazy danych i metod akcji [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (tworzenie, odczytywanie, aktualizowanie i usuwanie) jest znane jako *rusztowanie*.</span><span class="sxs-lookup"><span data-stu-id="96e37-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96e37-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96e37-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="96e37-497">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="96e37-498">Zainstaluj narzędzie do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="96e37-499">W systemie Linux wyeksportuj ścieżkę narzędzia do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="96e37-500">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96e37-501">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="96e37-502">Otwórz okno polecenia w katalogu projektu (katalog zawierający pliki *program.cs*, *Startup.cs* i *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="96e37-503">Zainstaluj narzędzie do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="96e37-504">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="96e37-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="96e37-505">Jeśli uruchomisz aplikację i klikniesz link do **filmu MVC** , zostanie wyświetlony komunikat o błędzie podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="96e37-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-506">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-507">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="96e37-508">Należy utworzyć bazę danych i użyć funkcji [migracji](xref:data/ef-mvc/migrations) EF Core, aby to zrobić.</span><span class="sxs-lookup"><span data-stu-id="96e37-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="96e37-509">Migracja umożliwia utworzenie bazy danych zgodnej z modelem danych i zaktualizowanie schematu bazy danych, gdy zmieni się model danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="96e37-510">Migracja początkowa</span><span class="sxs-lookup"><span data-stu-id="96e37-510">Initial migration</span></span>

<span data-ttu-id="96e37-511">W tej sekcji zostały wykonane następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="96e37-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="96e37-512">Dodawanie początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-512">Add an initial migration.</span></span>
* <span data-ttu-id="96e37-513">Zaktualizuj bazę danych przy użyciu początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-514">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="96e37-515">W menu **Narzędzia** wybierz kolejno pozycje **Menedżer pakietów NuGet** > **konsola Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="96e37-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="96e37-517">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96e37-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="96e37-518">`Add-Migration`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="96e37-519">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie.</span><span class="sxs-lookup"><span data-stu-id="96e37-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="96e37-520">`Initial`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="96e37-521">Można użyć dowolnej nazwy, ale według Konwencji, nazwy opisującej migrację.</span><span class="sxs-lookup"><span data-stu-id="96e37-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="96e37-522">Aby uzyskać więcej informacji, zobacz <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="96e37-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="96e37-523">`Update-Database`Polecenie uruchamia `Up` metodę w pliku *migrations/{Time-sygnatura} _InitialCreate. cs* , który tworzy bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-524">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="96e37-525">`ef migrations add InitialCreate`Polecenie generuje kod, aby utworzyć początkowy schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="96e37-526">Schemat bazy danych jest oparty na modelu określonym w `MvcMovieContext` klasie (w pliku *Data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="96e37-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="96e37-527">`InitialCreate`Argument jest nazwą migracji.</span><span class="sxs-lookup"><span data-stu-id="96e37-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="96e37-528">Można użyć dowolnej nazwy, ale według Konwencji została wybrana nazwa opisująca migrację.</span><span class="sxs-lookup"><span data-stu-id="96e37-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="96e37-529">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="96e37-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="96e37-530">ASP.NET Core jest skompilowany przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="96e37-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="96e37-531">Usługi (takie jak kontekst EF Core DB) są rejestrowane przy użyciu funkcji "DI" podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96e37-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="96e37-532">Składniki wymagające tych usług (takie jak Razor strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="96e37-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="96e37-533">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="96e37-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96e37-534">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96e37-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96e37-535">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu DI kontenera.</span><span class="sxs-lookup"><span data-stu-id="96e37-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="96e37-536">Przeanalizuj poniższą `Startup.ConfigureServices` metodę.</span><span class="sxs-lookup"><span data-stu-id="96e37-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="96e37-537">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="96e37-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="96e37-538">`MvcMovieContext`Współrzędne EF Core funkcje (tworzenie, Odczyt, aktualizowanie, usuwanie itp.) dla `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="96e37-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="96e37-539">Kontekst danych ( `MvcMovieContext` ) pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="96e37-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="96e37-540">Kontekst danych określa, które jednostki są uwzględnione w modelu danych:</span><span class="sxs-lookup"><span data-stu-id="96e37-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="96e37-541">Poprzedni kod tworzy właściwość [nieogólnymi \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="96e37-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="96e37-542">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96e37-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="96e37-543">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="96e37-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="96e37-544">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="96e37-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="96e37-545">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="96e37-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96e37-546">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96e37-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="96e37-547">Utworzono kontekst bazy danych i zarejestrowano go przy użyciu DI Container.</span><span class="sxs-lookup"><span data-stu-id="96e37-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="96e37-548">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="96e37-548">Test the app</span></span>

* <span data-ttu-id="96e37-549">Uruchom aplikację i Dołącz `/Movies` do adresu URL w przeglądarce ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="96e37-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="96e37-550">Jeśli zostanie wyświetlony wyjątek bazy danych podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="96e37-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="96e37-551">Pominięto [krok migracji](#pmc).</span><span class="sxs-lookup"><span data-stu-id="96e37-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="96e37-552">Przetestuj link **tworzenia** .</span><span class="sxs-lookup"><span data-stu-id="96e37-552">Test the **Create** link.</span></span> <span data-ttu-id="96e37-553">Wprowadź i prześlij dane.</span><span class="sxs-lookup"><span data-stu-id="96e37-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="96e37-554">W polu nie można wprowadzać przecinków dziesiętnych `Price` .</span><span class="sxs-lookup"><span data-stu-id="96e37-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="96e37-555">Aby zapewnić obsługę [walidacji jQuery](https://jqueryvalidation.org/) dla ustawień regionalnych innych niż angielski, które używają przecinka (",") dla punktu dziesiętnego i dla formatów nieUS-Englishych, aplikacja musi być globalna.</span><span class="sxs-lookup"><span data-stu-id="96e37-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="96e37-556">Aby uzyskać instrukcje dotyczące globalizacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="96e37-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="96e37-557">Przetestuj linki **Edytuj**, **Szczegóły** i **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="96e37-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="96e37-558">Zapoznaj się z `Startup` klasą:</span><span class="sxs-lookup"><span data-stu-id="96e37-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="96e37-559">Poprzedni wyróżniony kod pokazuje kontekst bazy danych filmu dodawany do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="96e37-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="96e37-560">`services.AddDbContext<MvcMovieContext>(options =>` Określa bazę danych, która ma być używana, oraz parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="96e37-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="96e37-561">`=>` jest [operatorem lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="96e37-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="96e37-562">Otwórz plik *controllers/MoviesController. cs* i zapoznaj się z konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="96e37-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="96e37-563">Konstruktor używa [iniekcji zależności](xref:fundamentals/dependency-injection) do iniekcji kontekstu bazy danych ( `MvcMovieContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="96e37-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="96e37-564">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="96e37-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="96e37-565">Modele silnie wpisane i @model słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="96e37-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="96e37-566">Wcześniej w tym samouczku pokazano, jak kontroler może przekazać dane lub obiekty do widoku przy użyciu `ViewData` słownika.</span><span class="sxs-lookup"><span data-stu-id="96e37-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="96e37-567">`ViewData`Słownik jest obiektem dynamicznym, który zapewnia wygodny, późny sposób przekazywania informacji do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="96e37-568">MVC oferuje również możliwość przekazywania obiektów modelu silnie typu do widoku.</span><span class="sxs-lookup"><span data-stu-id="96e37-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="96e37-569">Takie silnie wpisane podejście umożliwia lepsze sprawdzanie czasu kompilowania kodu.</span><span class="sxs-lookup"><span data-stu-id="96e37-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="96e37-570">Mechanizm tworzenia szkieletów korzysta z tego podejścia (czyli przekazywania modelu silnie określonego typu) z `MoviesController` klasą i widokami, gdy utworzyły metody i widoki.</span><span class="sxs-lookup"><span data-stu-id="96e37-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="96e37-571">Przejrzyj wygenerowaną `Details` metodę w pliku *controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="96e37-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="96e37-572">`id`Parametr jest zazwyczaj przesyłany jako dane trasy.</span><span class="sxs-lookup"><span data-stu-id="96e37-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="96e37-573">Na przykład `https://localhost:5001/movies/details/1` zestawy:</span><span class="sxs-lookup"><span data-stu-id="96e37-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="96e37-574">Kontroler do `movies` kontrolera (pierwszy segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="96e37-575">Akcja do `details` (drugi segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="96e37-576">Identyfikator 1 (ostatni segment adresu URL).</span><span class="sxs-lookup"><span data-stu-id="96e37-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="96e37-577">Można również przekazać `id` za pomocą ciągu zapytania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="96e37-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="96e37-578">`id`Parametr jest zdefiniowany jako [typ dopuszczający wartość null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) w przypadku, gdy nie podano wartości identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="96e37-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="96e37-579">[Wyrażenie lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) jest przesyłane do, `FirstOrDefaultAsync` Aby wybrać jednostki filmu, które pasują do wartości danych trasy lub ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="96e37-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="96e37-580">Jeśli film zostanie znaleziony, wystąpienie `Movie` modelu jest przesyłane do `Details` widoku:</span><span class="sxs-lookup"><span data-stu-id="96e37-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="96e37-581">Zapoznaj się z zawartością pliku *widoki/filmy/szczegóły. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="96e37-582">Dołączając `@model` instrukcję w górnej części pliku widoku, można określić typ obiektu, którego oczekuje widok.</span><span class="sxs-lookup"><span data-stu-id="96e37-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="96e37-583">Po utworzeniu kontrolera filmu Poniższa `@model` instrukcja została automatycznie uwzględniona w górnej części pliku *details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="96e37-584">Ta `@model` dyrektywa pozwala uzyskać dostęp do filmu, który kontroler przeszedł do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="96e37-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="96e37-585">Na przykład w widoku *details. cshtml* kod przekazuje każde pole filmu do `DisplayNameFor` `DisplayFor` pomocników HTML z obiektem o jednoznacznie określonym typie `Model` .</span><span class="sxs-lookup"><span data-stu-id="96e37-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="96e37-586">`Create`Metody i `Edit` i widoki również przekazują `Movie` obiekt modelu.</span><span class="sxs-lookup"><span data-stu-id="96e37-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="96e37-587">Sprawdź widok *index. cshtml* i `Index` metodę w kontrolerze filmów.</span><span class="sxs-lookup"><span data-stu-id="96e37-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="96e37-588">Zwróć uwagę, jak kod tworzy `List` obiekt, gdy wywołuje `View` metodę.</span><span class="sxs-lookup"><span data-stu-id="96e37-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="96e37-589">Kod przekazuje tę `Movies` listę z `Index` metody akcji do widoku:</span><span class="sxs-lookup"><span data-stu-id="96e37-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="96e37-590">Podczas tworzenia kontrolera filmów zostanie automatycznie uwzględniona następująca `@model` instrukcja w górnej części pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="96e37-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="96e37-591">`@model`Dyrektywa pozwala uzyskać dostęp do listy filmów przekazaną przez kontroler do widoku przy użyciu `Model` jednoznacznie określonego obiektu.</span><span class="sxs-lookup"><span data-stu-id="96e37-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="96e37-592">Na przykład w widoku *index. cshtml* kod przechodzi przez filmy z `foreach` instrukcją względem obiektu silnie wpisanego `Model` :</span><span class="sxs-lookup"><span data-stu-id="96e37-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="96e37-593">Ponieważ `Model` obiekt jest silnie określony (jako `IEnumerable<Movie>` obiekt), każdy element w pętli jest wpisywany jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="96e37-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="96e37-594">Dzięki temu możesz uzyskać kontrolę czasu kompilowania kodu:</span><span class="sxs-lookup"><span data-stu-id="96e37-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96e37-595">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96e37-595">Additional resources</span></span>

* [<span data-ttu-id="96e37-596">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="96e37-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="96e37-597">Globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="96e37-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="96e37-598">[Poprzednie dodanie widoku](adding-view.md) 
>  [Następna praca z bazą danych](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="96e37-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
