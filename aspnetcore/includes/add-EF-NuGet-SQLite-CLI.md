<span data-ttu-id="5addd-101">Uruchom następujące polecenia .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="5addd-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="5addd-102">Poprzednie polecenia dodają:</span><span class="sxs-lookup"><span data-stu-id="5addd-102">The preceding commands add:</span></span>

* <span data-ttu-id="5addd-103">[Narzędzie do rusztowania aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="5addd-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="5addd-104">Podstawowe narzędzia frameworka dla interfejsu wiersza polecenia .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5addd-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="5addd-105">Dostawca EF Core SQLite, który instaluje pakiet EF Core jako zależność.</span><span class="sxs-lookup"><span data-stu-id="5addd-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="5addd-106">Paczki potrzebne do rusztowania: `Microsoft.VisualStudio.Web.CodeGeneration.Design` i `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="5addd-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="5addd-107">Aby uzyskać wskazówki dotyczące konfiguracji wielu środowisk, która umożliwia aplikacji <xref:fundamentals/environments#environment-based-startup-class-and-methods>konfigurowanie kontekstów bazy danych według środowiska, zobacz .</span><span class="sxs-lookup"><span data-stu-id="5addd-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]