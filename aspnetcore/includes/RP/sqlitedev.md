### <a name="use-sqlite-for-development-sql-server-for-production"></a>Użyj SQLite do tworzenia, SQL Server dla produkcji

Po wybraniu SQLite, szablon wygenerowany kod jest gotowy do rozwoju. Poniższy kod pokazuje, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> jak wstrzyknąć do uruchamiania. `IWebHostEnvironment`jest wstrzykiwany, więc `ConfigureServices` można użyć SQLite w rozwoju i SQL Server w produkcji.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
