::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Utwórz folder *danych* .

Dodaj następującą `MvcMovieContext` klasę do folderu *danych* :  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodaj parametry połączenia z bazą danych

Dodaj parametry połączenia do *appsettings.jsw* pliku:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Dodawanie pakietów NuGet i narzędzi EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Kompiluj projekt jako sprawdzenie błędów kompilatora.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dodaj następującą `MvcMovieContext` klasę do folderu *models* :  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodaj parametry połączenia z bazą danych

Dodaj parametry połączenia do *appsettings.jsw* pliku:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Dodaj wymagane pakiety NuGet

Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Kompiluj projekt jako sprawdzenie pod kątem błędów.
::: moniker-end