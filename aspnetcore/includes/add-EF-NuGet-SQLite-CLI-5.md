<span data-ttu-id="652fb-101">Uruchom następujące polecenia środowiska uruchomieniowego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="652fb-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 5.0.100-rc.2.20479.15
dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.100-rc.2.20479.15
dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
dotnet add package Microsoft.Extensions.Logging.Debug -v 5.0.0-*
```

<span data-ttu-id="652fb-102">Powyższe polecenia powodują dodanie:</span><span class="sxs-lookup"><span data-stu-id="652fb-102">The preceding commands add:</span></span>

* <span data-ttu-id="652fb-103">Narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="652fb-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="652fb-104">EF Core narzędzia dla interfejsu wiersza polecenia platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="652fb-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="652fb-105">EF Core dostawca oprogramowania SQLite, który instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="652fb-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="652fb-106">Pakiety wymagające tworzenia szkieletów: `Microsoft.VisualStudio.Web.CodeGeneration.Design` i `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="652fb-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="652fb-107">Aby uzyskać wskazówki dotyczące wielu konfiguracji środowiska, które umożliwiają aplikacji skonfigurowanie kontekstów bazy danych według środowiska, zobacz <xref:fundamentals/environments#environment-based-startup-class-and-methods> .</span><span class="sxs-lookup"><span data-stu-id="652fb-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
