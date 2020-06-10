<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Dodawanie pakietów NuGet i narzędzi EF

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Dodaj klasę kontekstu bazy danych

W projekcie RazorPagesMovie Utwórz nowy folder o nazwie *dane*. Dodaj następującą `RazorPagesMovieContext` klasę do folderu *danych* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Poprzedni kod tworzy `DbSet` Właściwość zestawu jednostek. W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli. Kod nie zostanie skompilowany do momentu dodania zależności w późniejszym kroku.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Dodaj parametry połączenia z bazą danych

Dodaj parametry połączenia do pliku *appSettings. JSON* , jak pokazano w następującym wyróżnionym kodzie:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Dodaj wymagane pakiety NuGet

Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core, aby dodać program SQLite i CodeGeneration. Design do projektu:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design`Pakiet jest wymagany do tworzenia szkieletów.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Rejestrowanie kontekstu bazy danych

Dodaj następujące `using` instrukcje w górnej części *Startup.cs*:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Zarejestruj kontekst bazy danych z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Kompiluj projekt jako sprawdzenie pod kątem błędów.

::: moniker-end
