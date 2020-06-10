---
title: Część 4, Razor strony z EF Core w ASP.NET Core migracji
author: rick-anderson
description: Część 4 Razor stron i Entity Framework serii samouczków.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/migrations
ms.openlocfilehash: 74fe8771718647c3adf8288a72b11c30fb097a63
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652636"
---
# <a name="part-4-razor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="3398e-103">Część 4 Razor strony z migracjami EF Core w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3398e-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="3398e-104">Autorzy [Dykstra](https://github.com/tdykstra), [Jan P Kowalski](https://twitter.com/thereformedprog)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3398e-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3398e-105">W tym samouczku przedstawiono funkcję migracji EF Core na potrzeby zarządzania zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="3398e-106">Po opracowaniu nowej aplikacji model danych zmienia się często.</span><span class="sxs-lookup"><span data-stu-id="3398e-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="3398e-107">Za każdym razem, gdy model ulegnie zmianie, model nie jest zsynchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="3398e-108">Ta seria samouczków została uruchomiona przez skonfigurowanie Entity Framework, aby utworzyć bazę danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3398e-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="3398e-109">Za każdym razem, gdy model danych ulegnie zmianie, należy usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="3398e-110">Przy następnym uruchomieniu aplikacji wywołanie `EnsureCreated` ponownego tworzenia bazy danych jest zgodne z nowym modelem danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="3398e-111">`DbInitializer`Następnie zostanie uruchomiona nowa baza danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="3398e-112">Takie podejście do utrzymywania synchronizacji bazy danych z modelem danych działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3398e-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="3398e-113">Gdy aplikacja działa w środowisku produkcyjnym, zazwyczaj przechowuje dane, które muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="3398e-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="3398e-114">Aplikacja nie może rozpocząć pracy z testową bazą danych przy każdej zmianie (na przykład dodanie nowej kolumny).</span><span class="sxs-lookup"><span data-stu-id="3398e-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="3398e-115">Funkcja migracji EF Core rozwiązuje ten problem przez włączenie EF Core do zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="3398e-116">Zamiast upuszczania i ponownego tworzenia bazy danych, gdy zmieni się model danych, migracja aktualizuje schemat i zachowuje istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="3398e-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="3398e-117">Porzuć bazę danych</span><span class="sxs-lookup"><span data-stu-id="3398e-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3398e-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3398e-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3398e-119">Użyj **Eksplorator obiektów SQL Server** (SSOX), aby usunąć bazę danych, lub uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="3398e-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3398e-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3398e-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3398e-121">Uruchom następujące polecenie w wierszu polecenia, aby zainstalować program EF CLI:</span><span class="sxs-lookup"><span data-stu-id="3398e-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="3398e-122">W wierszu polecenia przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="3398e-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="3398e-123">Folder projektu zawiera plik *ContosoUniversity. csproj* .</span><span class="sxs-lookup"><span data-stu-id="3398e-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="3398e-124">Usuń plik *cu. DB* lub uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3398e-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="3398e-125">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="3398e-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3398e-126">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3398e-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3398e-127">Uruchom następujące polecenia w obszarze PMC:</span><span class="sxs-lookup"><span data-stu-id="3398e-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3398e-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3398e-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3398e-129">Upewnij się, że wiersz polecenia znajduje się w folderze projektu, i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3398e-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="3398e-130">Metody w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="3398e-130">Up and Down methods</span></span>

<span data-ttu-id="3398e-131">EF Core `migrations add` polecenie wygenerowało kod, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="3398e-132">Ten kod migracji znajduje się w pliku *migrations \<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="3398e-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3398e-133">`Up`Metoda `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="3398e-134">`Down`Metoda usuwa je, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3398e-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="3398e-135">Poprzedni kod jest przeznaczony dla początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="3398e-136">Kod:</span><span class="sxs-lookup"><span data-stu-id="3398e-136">The code:</span></span>

* <span data-ttu-id="3398e-137">Zostało wygenerowane przez `migrations add InitialCreate` polecenie.</span><span class="sxs-lookup"><span data-stu-id="3398e-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="3398e-138">Jest wykonywane przez `database update` polecenie.</span><span class="sxs-lookup"><span data-stu-id="3398e-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="3398e-139">Tworzy bazę danych dla modelu danych określonego przez klasę kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="3398e-140">Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="3398e-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="3398e-141">Nazwa migracji może być dowolną prawidłową nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="3398e-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="3398e-142">Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="3398e-143">Na przykład migracja dodana do tabeli działu może być nazywana "adddepartments".</span><span class="sxs-lookup"><span data-stu-id="3398e-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="3398e-144">Tabela historii migracji</span><span class="sxs-lookup"><span data-stu-id="3398e-144">The migrations history table</span></span>

* <span data-ttu-id="3398e-145">Aby sprawdzić bazę danych, użyj SSOX lub narzędzia SQLite.</span><span class="sxs-lookup"><span data-stu-id="3398e-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="3398e-146">Zwróć uwagę na dodanie `__EFMigrationsHistory` tabeli.</span><span class="sxs-lookup"><span data-stu-id="3398e-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="3398e-147">W `__EFMigrationsHistory` tabeli znajdują się informacje o tym, które migracje zostały zastosowane do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="3398e-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="3398e-148">Wyświetlanie danych w `__EFMigrationsHistory` tabeli.</span><span class="sxs-lookup"><span data-stu-id="3398e-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="3398e-149">Pokazuje jeden wiersz dla pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="3398e-150">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="3398e-150">The data model snapshot</span></span>

<span data-ttu-id="3398e-151">Migracja tworzy *migawkę* bieżącego modelu danych w *migracji/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="3398e-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="3398e-152">Po dodaniu migracji, EF określa zmiany, porównując bieżący model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="3398e-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="3398e-153">Ponieważ plik migawek śledzi stan modelu danych, nie można usunąć migracji, usuwając `<timestamp>_<migrationname>.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="3398e-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="3398e-154">Aby wykonać kopię zapasową najnowszej migracji, należy użyć `migrations remove` polecenia.</span><span class="sxs-lookup"><span data-stu-id="3398e-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="3398e-155">To polecenie usuwa migrację i gwarantuje, że migawka zostanie prawidłowo zresetowana.</span><span class="sxs-lookup"><span data-stu-id="3398e-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="3398e-156">Aby uzyskać więcej informacji, zobacz [migracja programu dotnet EF Usuń](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="3398e-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="3398e-157">Usuń EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="3398e-157">Remove EnsureCreated</span></span>

<span data-ttu-id="3398e-158">Ta seria samouczków została uruchomiona przy użyciu `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="3398e-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="3398e-159">`EnsureCreated`nie tworzy tabeli historii migracji i dlatego nie można jej używać z migracjami.</span><span class="sxs-lookup"><span data-stu-id="3398e-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="3398e-160">Jest ona przeznaczona do testowania lub szybkiego tworzenia prototypów, w których baza danych została porzucona i wielokrotnie utworzona.</span><span class="sxs-lookup"><span data-stu-id="3398e-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="3398e-161">Od tego momentu samouczki będą korzystać z migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="3398e-162">W polu *Data/dbinitialize. cs*Dodaj komentarz do następującego wiersza:</span><span class="sxs-lookup"><span data-stu-id="3398e-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="3398e-163">Uruchom aplikację i sprawdź, czy baza danych została zainicjowana.</span><span class="sxs-lookup"><span data-stu-id="3398e-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="3398e-164">Stosowanie migracji w środowisku produkcyjnym</span><span class="sxs-lookup"><span data-stu-id="3398e-164">Applying migrations in production</span></span>

<span data-ttu-id="3398e-165">Zalecamy, aby aplikacje produkcyjne **nie** wywoływały [bazy danych. Przeprowadź migrację](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3398e-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="3398e-166">`Migrate`nie należy wywoływać z aplikacji wdrożonej w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="3398e-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="3398e-167">Jeśli aplikacja jest skalowana w poziomie wielu wystąpień serwera, trudno jest upewnić się, że aktualizacje schematu bazy danych nie występują z wielu serwerów lub powodują konflikt z dostępem do odczytu i zapisu.</span><span class="sxs-lookup"><span data-stu-id="3398e-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="3398e-168">Migracja bazy danych powinna odbywać się w ramach wdrożenia i w sposób kontrolowany.</span><span class="sxs-lookup"><span data-stu-id="3398e-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="3398e-169">Podejścia do migracji produkcyjnej bazy danych obejmują:</span><span class="sxs-lookup"><span data-stu-id="3398e-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="3398e-170">Używanie migracji do tworzenia skryptów SQL i korzystania ze skryptów SQL we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="3398e-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="3398e-171">Uruchamianie `dotnet ef database update` z poziomu kontrolowanego środowiska.</span><span class="sxs-lookup"><span data-stu-id="3398e-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3398e-172">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="3398e-172">Troubleshooting</span></span>

<span data-ttu-id="3398e-173">Jeśli aplikacja używa SQL Server LocalDB i wyświetla następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="3398e-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="3398e-174">Rozwiązanie może być uruchamiane z `dotnet ef database update` wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="3398e-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="3398e-175">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3398e-175">Additional resources</span></span>

* <span data-ttu-id="3398e-176">[Interfejs wiersza polecenia EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="3398e-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="3398e-177">Konsola menedżera pakietów (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="3398e-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="3398e-178">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="3398e-178">Next steps</span></span>

<span data-ttu-id="3398e-179">Następny samouczek kompiluje model danych, dodając właściwości jednostki i nowe jednostki.</span><span class="sxs-lookup"><span data-stu-id="3398e-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3398e-180">[Poprzedni samouczek](xref:data/ef-rp/sort-filter-page) 
>  [Następny samouczek](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="3398e-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3398e-181">W tym samouczku zostanie użyta funkcja migracji EF Core do zarządzania zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="3398e-182">Jeśli wystąpią problemy, których nie można rozwiązać, Pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="3398e-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="3398e-183">Po opracowaniu nowej aplikacji model danych zmienia się często.</span><span class="sxs-lookup"><span data-stu-id="3398e-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="3398e-184">Za każdym razem, gdy model ulegnie zmianie, model nie jest zsynchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="3398e-185">Ten samouczek został uruchomiony przez skonfigurowanie Entity Framework, aby utworzyć bazę danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="3398e-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="3398e-186">Za każdym razem, gdy zmienia się model danych:</span><span class="sxs-lookup"><span data-stu-id="3398e-186">Each time the data model changes:</span></span>

* <span data-ttu-id="3398e-187">Baza danych została porzucona.</span><span class="sxs-lookup"><span data-stu-id="3398e-187">The DB is dropped.</span></span>
* <span data-ttu-id="3398e-188">EF tworzy nowy, który jest zgodny z modelem.</span><span class="sxs-lookup"><span data-stu-id="3398e-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="3398e-189">Aplikacja wysienna bazę danych z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="3398e-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="3398e-190">Takie podejście do synchronizowania bazy danych z modelem dane działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3398e-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="3398e-191">Gdy aplikacja działa w środowisku produkcyjnym, zazwyczaj przechowuje dane, które muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="3398e-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="3398e-192">Aplikacja nie może rozpoczynać się od bazy danych testów za każdym razem, gdy zostanie wprowadzona zmiana (na przykład dodanie nowej kolumny).</span><span class="sxs-lookup"><span data-stu-id="3398e-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="3398e-193">Funkcja migracji EF Core rozwiązuje ten problem przez włączenie EF Core w celu zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="3398e-194">Zamiast upuszczania i ponownego tworzenia bazy danych, gdy zmieni się model, migracja aktualizuje schemat i zachowuje istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="3398e-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="3398e-195">Porzuć bazę danych</span><span class="sxs-lookup"><span data-stu-id="3398e-195">Drop the database</span></span>

<span data-ttu-id="3398e-196">Użyj **Eksplorator obiektów SQL Server** (SSOX) lub `database drop` polecenia:</span><span class="sxs-lookup"><span data-stu-id="3398e-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3398e-197">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3398e-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3398e-198">W **konsoli Menedżera pakietów** (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3398e-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="3398e-199">Uruchom `Get-Help about_EntityFrameworkCore` z PMC, aby uzyskać informacje pomocy.</span><span class="sxs-lookup"><span data-stu-id="3398e-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3398e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3398e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3398e-201">Otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="3398e-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="3398e-202">Folder projektu zawiera plik *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="3398e-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="3398e-203">Wprowadź następujące polecenie w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="3398e-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="3398e-204">Tworzenie początkowej migracji i aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="3398e-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="3398e-205">Kompilowanie projektu i Tworzenie pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3398e-206">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3398e-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3398e-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3398e-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="3398e-208">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="3398e-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="3398e-209">EF Core `migrations add` polecenie wygenerowało kod, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="3398e-210">Ten kod migracji znajduje się w pliku *migrations \<timestamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="3398e-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3398e-211">`Up`Metoda `InitialCreate` klasy tworzy tabele DB, które odpowiadają zestawom jednostek modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="3398e-212">`Down`Metoda usuwa je, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3398e-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="3398e-213">Migracja wywołuje `Up` metodę w celu zaimplementowania zmian modelu danych dla migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="3398e-214">Po wprowadzeniu polecenia w celu wycofania aktualizacji migracja wywołuje `Down` metodę.</span><span class="sxs-lookup"><span data-stu-id="3398e-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="3398e-215">Poprzedni kod jest przeznaczony dla początkowej migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="3398e-216">Ten kod został utworzony, gdy `migrations add InitialCreate` polecenie zostało uruchomione.</span><span class="sxs-lookup"><span data-stu-id="3398e-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="3398e-217">Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku.</span><span class="sxs-lookup"><span data-stu-id="3398e-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="3398e-218">Nazwa migracji może być dowolną prawidłową nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="3398e-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="3398e-219">Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="3398e-220">Na przykład migracja dodana do tabeli działu może być nazywana "adddepartments".</span><span class="sxs-lookup"><span data-stu-id="3398e-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="3398e-221">Jeśli migracja początkowa jest tworzona i baza danych istnieje:</span><span class="sxs-lookup"><span data-stu-id="3398e-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="3398e-222">Kod tworzenia bazy danych jest generowany.</span><span class="sxs-lookup"><span data-stu-id="3398e-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="3398e-223">Nie trzeba uruchamiać kodu tworzenia bazy danych, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="3398e-224">W przypadku uruchomienia kodu tworzenia bazy danych nie wprowadzono żadnych zmian, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="3398e-225">Gdy aplikacja zostanie wdrożona w nowym środowisku, należy uruchomić kod tworzenia bazy danych, aby utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="3398e-226">Wcześniej baza danych została porzucona i nie istnieje, dlatego migracji tworzy nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="3398e-227">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="3398e-227">The data model snapshot</span></span>

<span data-ttu-id="3398e-228">Migracje tworzą *migawkę* bieżącego schematu bazy danych w *migracji/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="3398e-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="3398e-229">Po dodaniu migracji, EF określa, co zmieniło się, porównując model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="3398e-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="3398e-230">Aby usunąć migrację, użyj następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="3398e-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3398e-231">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3398e-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3398e-232">Usuń migrację</span><span class="sxs-lookup"><span data-stu-id="3398e-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3398e-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3398e-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="3398e-234">Aby uzyskać więcej informacji, zobacz [migracja programu dotnet EF Usuń](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="3398e-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="3398e-235">Polecenie Usuń migracje powoduje usunięcie migracji i gwarantuje, że migawka zostanie prawidłowo zresetowana.</span><span class="sxs-lookup"><span data-stu-id="3398e-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="3398e-236">Usuń EnsureCreated i przetestuj aplikację</span><span class="sxs-lookup"><span data-stu-id="3398e-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="3398e-237">W przypadku wczesnego opracowywania programu `EnsureCreated` została użyta.</span><span class="sxs-lookup"><span data-stu-id="3398e-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="3398e-238">W tym samouczku zostaną użyte migracje.</span><span class="sxs-lookup"><span data-stu-id="3398e-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="3398e-239">`EnsureCreated`ma następujące ograniczenia:</span><span class="sxs-lookup"><span data-stu-id="3398e-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="3398e-240">Pomija migracje i tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="3398e-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="3398e-241">Nie tworzy tabeli migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="3398e-242">*Nie* można go używać z migracjami.</span><span class="sxs-lookup"><span data-stu-id="3398e-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="3398e-243">Jest przeznaczony do testowania lub szybkiego tworzenia prototypów, w których baza danych została porzucona i wielokrotnie utworzona.</span><span class="sxs-lookup"><span data-stu-id="3398e-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="3398e-244">Usuń `EnsureCreated` :</span><span class="sxs-lookup"><span data-stu-id="3398e-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="3398e-245">Uruchom aplikację i sprawdź, czy baza danych została zainicjowana.</span><span class="sxs-lookup"><span data-stu-id="3398e-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="3398e-246">Inspekcja bazy danych</span><span class="sxs-lookup"><span data-stu-id="3398e-246">Inspect the database</span></span>

<span data-ttu-id="3398e-247">Użyj **Eksplorator obiektów SQL Server** , aby sprawdzić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="3398e-248">Zwróć uwagę na dodanie `__EFMigrationsHistory` tabeli.</span><span class="sxs-lookup"><span data-stu-id="3398e-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="3398e-249">W `__EFMigrationsHistory` tabeli znajdują się informacje o tym, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3398e-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="3398e-250">Wyświetl dane w `__EFMigrationsHistory` tabeli, pokazując jeden wiersz dla pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="3398e-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="3398e-251">Ostatni dziennik w poprzednim przykładzie danych wyjściowych interfejsu wiersza polecenia przedstawia instrukcję INSERT, która tworzy ten wiersz.</span><span class="sxs-lookup"><span data-stu-id="3398e-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="3398e-252">Uruchom aplikację i sprawdź, czy wszystko działa.</span><span class="sxs-lookup"><span data-stu-id="3398e-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="3398e-253">Stosowanie migracji w środowisku produkcyjnym</span><span class="sxs-lookup"><span data-stu-id="3398e-253">Applying migrations in production</span></span>

<span data-ttu-id="3398e-254">Zalecamy, aby aplikacje produkcyjne **nie** wywoływały metody [Database. migruje](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3398e-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="3398e-255">`Migrate`nie należy wywoływać z aplikacji w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="3398e-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="3398e-256">Na przykład jeśli aplikacja została wdrożona w chmurze przy użyciu skalowania w poziomie (uruchomiono wiele wystąpień aplikacji).</span><span class="sxs-lookup"><span data-stu-id="3398e-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="3398e-257">Migracja bazy danych powinna odbywać się w ramach wdrożenia i w sposób kontrolowany.</span><span class="sxs-lookup"><span data-stu-id="3398e-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="3398e-258">Podejścia do migracji produkcyjnej bazy danych obejmują:</span><span class="sxs-lookup"><span data-stu-id="3398e-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="3398e-259">Używanie migracji do tworzenia skryptów SQL i korzystania ze skryptów SQL we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="3398e-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="3398e-260">Uruchamianie `dotnet ef database update` z poziomu kontrolowanego środowiska.</span><span class="sxs-lookup"><span data-stu-id="3398e-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="3398e-261">EF Core używa `__MigrationsHistory` tabeli, aby sprawdzić, czy migracja musi być uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="3398e-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="3398e-262">Jeśli baza danych jest aktualna, migracja nie jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="3398e-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3398e-263">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="3398e-263">Troubleshooting</span></span>

<span data-ttu-id="3398e-264">Pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="3398e-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="3398e-265">Aplikacja generuje następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="3398e-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="3398e-266">Rozwiązanie: Uruchom`dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="3398e-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="3398e-267">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3398e-267">Additional resources</span></span>

* [<span data-ttu-id="3398e-268">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="3398e-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="3398e-269">[Interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="3398e-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="3398e-270">Konsola menedżera pakietów (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="3398e-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="3398e-271">[Poprzedni](xref:data/ef-rp/sort-filter-page) 
>  [Dalej](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="3398e-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

