::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Utwórz folder *Dane.*

Dodaj następującą `MvcMovieContext` klasę do folderu *Dane:*  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Powyższy kod tworzy `DbSet` właściwość dla zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodawanie ciągu połączenia bazy danych

Dodaj parametry połączenia do pliku *appsettings.json:*

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Dodawanie pakietów NuGet i narzędzi EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych za pomocą `Startup.ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w pliku .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Skompiluj projekt jako sprawdzanie błędów kompilatora.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dodaj następującą `MvcMovieContext` klasę do folderu *Modele:*  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Powyższy kod tworzy `DbSet` właściwość dla zestawu jednostek. W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodawanie ciągu połączenia bazy danych

Dodaj parametry połączenia do pliku *appsettings.json:*

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Dodawanie wymaganych pakietów NuGet

Uruchom następujące polecenie .NET Core CLI, aby dodać SQLite i CodeGeneration.Design do projektu:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Pakiet `Microsoft.VisualStudio.Web.CodeGeneration.Design` jest wymagany do rusztowania.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs:*

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych za pomocą `Startup.ConfigureServices` [kontenera iniekcji zależności](xref:fundamentals/dependency-injection) w pliku .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Skompiluj projekt jako sprawdzanie błędów.
::: moniker-end