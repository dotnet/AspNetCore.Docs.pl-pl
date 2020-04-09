Uruchom następujące polecenia .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Poprzednie polecenia dodają:

* [Narzędzie do rusztowania aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Podstawowe narzędzia frameworka dla interfejsu wiersza polecenia .NET Core.
* Dostawca EF Core SQLite, który instaluje pakiet EF Core jako zależność.
* Paczki potrzebne do rusztowania: `Microsoft.VisualStudio.Web.CodeGeneration.Design` i `Microsoft.EntityFrameworkCore.SqlServer`.

Aby uzyskać wskazówki dotyczące konfiguracji wielu środowisk, która umożliwia aplikacji <xref:fundamentals/environments#environment-based-startup-class-and-methods>konfigurowanie kontekstów bazy danych według środowiska, zobacz .

[!INCLUDE[](~/includes/scaffoldTFM.md)]