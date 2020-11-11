Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

Powyższe polecenia powodują dodanie:

* Narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* EF Core narzędzia dla interfejsu wiersza polecenia platformy .NET.
* EF Core dostawca oprogramowania SQLite, który instaluje pakiet EF Core jako zależność.
* Pakiety wymagające tworzenia szkieletów: `Microsoft.VisualStudio.Web.CodeGeneration.Design` i `Microsoft.EntityFrameworkCore.SqlServer` .

Aby uzyskać wskazówki dotyczące wielu konfiguracji środowiska, które umożliwiają aplikacji skonfigurowanie kontekstów bazy danych według środowiska, zobacz <xref:fundamentals/environments#environment-based-startup-class-and-methods> .

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
