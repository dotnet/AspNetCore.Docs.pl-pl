<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="e5f66-101">Dodawanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f66-101">Add a database context class</span></span>

<span data-ttu-id="e5f66-102">W projekcie RazorPagesMovie utwórz nowy folder o nazwie *Data*.</span><span class="sxs-lookup"><span data-stu-id="e5f66-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="e5f66-103">Dodaj następującą `RazorPagesMovieContext` klasę do folderu *Dane:*</span><span class="sxs-lookup"><span data-stu-id="e5f66-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e5f66-104">Powyższy kod tworzy `DbSet` właściwość dla zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="e5f66-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e5f66-105">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="e5f66-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e5f66-106">Kod nie będzie kompilować, dopóki zależności nie zostaną dodane w późniejszym kroku.</span><span class="sxs-lookup"><span data-stu-id="e5f66-106">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e5f66-107">Dodawanie ciągu połączenia bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f66-107">Add a database connection string</span></span>

<span data-ttu-id="e5f66-108">Dodaj parametry połączenia do pliku *appsettings.json,* jak pokazano w poniższym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="e5f66-108">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e5f66-109">Dodawanie pakietów NuGet i narzędzi EF</span><span class="sxs-lookup"><span data-stu-id="e5f66-109">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e5f66-110">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f66-110">Register the database context</span></span>

<span data-ttu-id="e5f66-111">Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="e5f66-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e5f66-112">Zarejestruj kontekst bazy danych za pomocą `Startup.ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w pliku .</span><span class="sxs-lookup"><span data-stu-id="e5f66-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="e5f66-113">Dodawanie wymaganych pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="e5f66-113">Add required NuGet packages</span></span>

<span data-ttu-id="e5f66-114">Uruchom następujące polecenie .NET Core CLI, aby dodać SQLite i CodeGeneration.Design do projektu:</span><span class="sxs-lookup"><span data-stu-id="e5f66-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="e5f66-115">Pakiet `Microsoft.VisualStudio.Web.CodeGeneration.Design` jest wymagany do rusztowania.</span><span class="sxs-lookup"><span data-stu-id="e5f66-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e5f66-116">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="e5f66-116">Register the database context</span></span>

<span data-ttu-id="e5f66-117">Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="e5f66-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e5f66-118">Zarejestruj kontekst bazy danych za pomocą `Startup.ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w pliku .</span><span class="sxs-lookup"><span data-stu-id="e5f66-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="e5f66-119">Skompiluj projekt jako sprawdzanie błędów.</span><span class="sxs-lookup"><span data-stu-id="e5f66-119">Build the project as a check for errors.</span></span>

::: moniker-end
