::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="e5f75-101">Utwórz folder *Dane.*</span><span class="sxs-lookup"><span data-stu-id="e5f75-101">Create a *Data* folder.</span></span>

<span data-ttu-id="e5f75-102">Dodaj następującą `MvcMovieContext` klasę do folderu *Dane:*</span><span class="sxs-lookup"><span data-stu-id="e5f75-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="e5f75-103">Powyższy kod tworzy `DbSet` właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e5f75-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e5f75-104">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e5f75-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e5f75-105">Dodawanie ciągu połączenia bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f75-105">Add a database connection string</span></span>

<span data-ttu-id="e5f75-106">Dodaj parametry połączenia do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="e5f75-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e5f75-107">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="e5f75-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e5f75-108">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f75-108">Register the database context</span></span>

<span data-ttu-id="e5f75-109">Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="e5f75-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e5f75-110">Zarejestruj kontekst bazy danych za pomocą `Startup.ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w pliku .</span><span class="sxs-lookup"><span data-stu-id="e5f75-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="e5f75-111">Skompiluj projekt jako sprawdzanie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="e5f75-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5f75-112">Dodaj następującą `MvcMovieContext` klasę do folderu *Modele:*</span><span class="sxs-lookup"><span data-stu-id="e5f75-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="e5f75-113">Powyższy kod tworzy `DbSet` właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e5f75-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e5f75-114">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e5f75-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e5f75-115">Dodawanie ciągu połączenia bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f75-115">Add a database connection string</span></span>

<span data-ttu-id="e5f75-116">Dodaj parametry połączenia do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="e5f75-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="e5f75-117">Dodawanie wymaganych pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="e5f75-117">Add required NuGet packages</span></span>

<span data-ttu-id="e5f75-118">Uruchom następujące polecenie .NET Core CLI, aby dodać SQLite i CodeGeneration.Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="e5f75-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="e5f75-119">Pakiet `Microsoft.VisualStudio.Web.CodeGeneration.Design` jest wymagany do rusztowania.</span><span class="sxs-lookup"><span data-stu-id="e5f75-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e5f75-120">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f75-120">Register the database context</span></span>

<span data-ttu-id="e5f75-121">Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="e5f75-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e5f75-122">Zarejestruj kontekst bazy danych za pomocą `Startup.ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w pliku .</span><span class="sxs-lookup"><span data-stu-id="e5f75-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="e5f75-123">Skompiluj projekt jako sprawdzanie błędów.</span><span class="sxs-lookup"><span data-stu-id="e5f75-123">Build the project as a check for errors.</span></span>
::: moniker-end