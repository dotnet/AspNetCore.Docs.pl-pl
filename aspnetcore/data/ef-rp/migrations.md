---
title: Strony brzytwy z EF Core w rdzeniu ASP.NET - Migracje - 4 z 8
author: rick-anderson
description: W tym samouczku można rozpocząć korzystanie z funkcji migracji EF Core do zarządzania zmianami modelu danych w aplikacji ASP.NET Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 86fd83c898fce8e121e4d259aaca12c59591e606
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656535"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="bd838-103">Strony brzytwy z EF Core w rdzeniu ASP.NET - Migracje - 4 z 8</span><span class="sxs-lookup"><span data-stu-id="bd838-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="bd838-104">Przez [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)i Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd838-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd838-105">W tym samouczku przedstawiono funkcję migracji ef core do zarządzania zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="bd838-106">Po opracowaniu nowej aplikacji model danych zmienia się często.</span><span class="sxs-lookup"><span data-stu-id="bd838-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="bd838-107">Za każdym razem, gdy zmienia się model, model jest zsynchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="bd838-108">Ta seria samouczków rozpoczęła się od skonfigurowania entity framework do utworzenia bazy danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="bd838-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="bd838-109">Za każdym razem, gdy zmienia się model danych, należy upuścić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="bd838-110">Przy następnym uruchomieniu aplikacji wywołanie `EnsureCreated` ponownie tworzy bazę danych, aby dopasować nowy model danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="bd838-111">Klasa `DbInitializer` następnie uruchamia się do materiału siewnego nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="bd838-112">Takie podejście do utrzymywania bazy danych w synchronizacji z modelem danych działa dobrze, dopóki nie wdrożysz aplikacji w procesach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="bd838-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="bd838-113">Gdy aplikacja jest uruchomiona w produkcji, zwykle przechowuje dane, które muszą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="bd838-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="bd838-114">Aplikacja nie może rozpocząć się od testowej bazy danych za każdym razem, gdy zostanie wniesiena zmiana (na przykład dodanie nowej kolumny).</span><span class="sxs-lookup"><span data-stu-id="bd838-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="bd838-115">Funkcja Migracji rdzenia EF rozwiązuje ten problem, umożliwiając EF Core zaktualizować schemat bazy danych zamiast tworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="bd838-116">Zamiast upuszczania i ponownego tworzenia bazy danych po zmianie modelu danych, migracje aktualizuje schemat i zachowuje istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="bd838-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="bd838-117">Upuść bazę danych</span><span class="sxs-lookup"><span data-stu-id="bd838-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd838-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd838-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd838-119">Użyj **programu SQL Server Object Explorer** (SSOX), aby usunąć bazę danych, lub uruchom następujące polecenie w konsoli Menedżera **pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="bd838-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd838-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd838-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bd838-121">Uruchom następujące polecenie w wierszu polecenia, aby zainstalować ef cli:</span><span class="sxs-lookup"><span data-stu-id="bd838-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="bd838-122">W wierszu polecenia przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="bd838-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="bd838-123">Folder projektu zawiera plik *ContosoUniversity.csproj.*</span><span class="sxs-lookup"><span data-stu-id="bd838-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="bd838-124">Usuń plik *CU.db* lub uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="bd838-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="bd838-125">Tworzenie migracji początkowej</span><span class="sxs-lookup"><span data-stu-id="bd838-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd838-126">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd838-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd838-127">Uruchom następujące polecenia w PMC:</span><span class="sxs-lookup"><span data-stu-id="bd838-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd838-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd838-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd838-129">Upewnij się, że wiersz polecenia znajduje się w folderze projektu i uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="bd838-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="bd838-130">Metody w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="bd838-130">Up and Down methods</span></span>

<span data-ttu-id="bd838-131">Polecenie EF `migrations add` Core wygenerowało kod do utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="bd838-132">Ten kod migracji znajduje się w pliku *sygnatury czasowej\<migracji>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="bd838-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="bd838-133">Metoda `Up` `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="bd838-134">Metoda `Down` usuwa je, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="bd838-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="bd838-135">Poprzedni kod dotyczy migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="bd838-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="bd838-136">Kod:</span><span class="sxs-lookup"><span data-stu-id="bd838-136">The code:</span></span>

* <span data-ttu-id="bd838-137">Został wygenerowany `migrations add InitialCreate` przez polecenie.</span><span class="sxs-lookup"><span data-stu-id="bd838-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="bd838-138">Jest wykonywany `database update` przez polecenie.</span><span class="sxs-lookup"><span data-stu-id="bd838-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="bd838-139">Tworzy bazę danych dla modelu danych określonego przez klasę kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="bd838-140">Parametr nazwa migracji ("InitialCreate" w przykładzie) jest używany dla nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="bd838-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="bd838-141">Nazwa migracji może być dowolną prawidłową nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="bd838-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="bd838-142">Najlepiej wybrać słowo lub frazę, która podsumowuje to, co jest wykonywane podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="bd838-143">Na przykład migracji, która dodała tabelę działu może być nazywany "AddDepartmentTable."</span><span class="sxs-lookup"><span data-stu-id="bd838-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="bd838-144">Tabela historii migracji</span><span class="sxs-lookup"><span data-stu-id="bd838-144">The migrations history table</span></span>

* <span data-ttu-id="bd838-145">Użyj SSOX lub narzędzia SQLite, aby sprawdzić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="bd838-146">Zwróć uwagę na `__EFMigrationsHistory` dodanie tabeli.</span><span class="sxs-lookup"><span data-stu-id="bd838-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="bd838-147">Tabela `__EFMigrationsHistory` śledzi, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="bd838-148">Służy do wyświetlania danych w `__EFMigrationsHistory` tabeli.</span><span class="sxs-lookup"><span data-stu-id="bd838-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="bd838-149">Pokazuje jeden wiersz dla pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="bd838-150">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="bd838-150">The data model snapshot</span></span>

<span data-ttu-id="bd838-151">Migracje tworzy *migawkę* bieżącego modelu danych w *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="bd838-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="bd838-152">Po dodaniu migracji EF określa, co się zmieniło, porównując bieżący model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="bd838-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="bd838-153">Ponieważ plik migawki śledzi stan modelu danych, nie można usunąć migracji, usuwając `<timestamp>_<migrationname>.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="bd838-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="bd838-154">Aby wycofać najnowszą migrację, `migrations remove` musisz użyć polecenia.</span><span class="sxs-lookup"><span data-stu-id="bd838-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="bd838-155">To polecenie usuwa migrację i zapewnia poprawne zresetowanie migawki.</span><span class="sxs-lookup"><span data-stu-id="bd838-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="bd838-156">Aby uzyskać więcej informacji, zobacz [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="bd838-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="bd838-157">Usuń EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="bd838-157">Remove EnsureCreated</span></span>

<span data-ttu-id="bd838-158">Ta seria samouczków `EnsureCreated`rozpoczęta przy użyciu programu .</span><span class="sxs-lookup"><span data-stu-id="bd838-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="bd838-159">`EnsureCreated`nie tworzy tabeli historii migracji i dlatego nie można jej używać z migracjami.</span><span class="sxs-lookup"><span data-stu-id="bd838-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="bd838-160">Jest przeznaczony do testowania lub szybkiego prototypowania, gdzie baza danych jest często odrzucana i często odtwarzana.</span><span class="sxs-lookup"><span data-stu-id="bd838-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="bd838-161">Od tego momentu samouczki będą używać migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="bd838-162">W *pliku Data/DBInitializer.cs*wyrzuć następujący wiersz:</span><span class="sxs-lookup"><span data-stu-id="bd838-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="bd838-163">Uruchom aplikację i sprawdź, czy baza danych jest rozstawiona.</span><span class="sxs-lookup"><span data-stu-id="bd838-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="bd838-164">Stosowanie migracji w produkcji</span><span class="sxs-lookup"><span data-stu-id="bd838-164">Applying migrations in production</span></span>

<span data-ttu-id="bd838-165">Zaleca się, że aplikacje produkcyjne **nie** [wywołać Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bd838-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="bd838-166">`Migrate`nie powinien być wywoływany z aplikacji, która jest wdrażana w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="bd838-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="bd838-167">Jeśli aplikacja jest skalowana w poziomie do wielu wystąpień serwera, trudno jest upewnić się, że aktualizacje schematu bazy danych nie nastąpi z wielu serwerów lub konflikt z dostępem do odczytu/zapisu.</span><span class="sxs-lookup"><span data-stu-id="bd838-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="bd838-168">Migracja bazy danych powinna być wykonywana w ramach wdrażania i w sposób kontrolowany.</span><span class="sxs-lookup"><span data-stu-id="bd838-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="bd838-169">Metody migracji danych produkcyjnych obejmują:</span><span class="sxs-lookup"><span data-stu-id="bd838-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="bd838-170">Używanie migracji do tworzenia skryptów SQL i używania skryptów SQL we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="bd838-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="bd838-171">Uruchamianie `dotnet ef database update` z kontrolowanego środowiska.</span><span class="sxs-lookup"><span data-stu-id="bd838-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bd838-172">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="bd838-172">Troubleshooting</span></span>

<span data-ttu-id="bd838-173">Jeśli aplikacja korzysta z usługi SQL Server LocalDB i wyświetla następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="bd838-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="bd838-174">Rozwiązaniem może być `dotnet ef database update` uruchomienie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="bd838-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="bd838-175">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bd838-175">Additional resources</span></span>

* <span data-ttu-id="bd838-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="bd838-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="bd838-177">Konsola menedżera pakietów (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="bd838-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="bd838-178">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="bd838-178">Next steps</span></span>

<span data-ttu-id="bd838-179">Następny samouczek tworzy model danych, dodając właściwości jednostki i nowe jednostki.</span><span class="sxs-lookup"><span data-stu-id="bd838-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bd838-180">[Poprzedni samouczek](xref:data/ef-rp/sort-filter-page)
> [Następny samouczek](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="bd838-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd838-181">W tym samouczku używana jest funkcja migracji ef core do zarządzania zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="bd838-182">Jeśli napotkasz problemy, których nie możesz rozwiązać, pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="bd838-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="bd838-183">Po opracowaniu nowej aplikacji model danych zmienia się często.</span><span class="sxs-lookup"><span data-stu-id="bd838-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="bd838-184">Za każdym razem, gdy zmienia się model, model jest zsynchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="bd838-185">Ten samouczek rozpoczęty przez skonfigurowanie entity framework do tworzenia bazy danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="bd838-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="bd838-186">Za każdym razem, gdy zmienia się model danych:</span><span class="sxs-lookup"><span data-stu-id="bd838-186">Each time the data model changes:</span></span>

* <span data-ttu-id="bd838-187">Baza danych zostanie porzucona.</span><span class="sxs-lookup"><span data-stu-id="bd838-187">The DB is dropped.</span></span>
* <span data-ttu-id="bd838-188">EF tworzy nowy, który pasuje do modelu.</span><span class="sxs-lookup"><span data-stu-id="bd838-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="bd838-189">Aplikacja nasiona DB z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="bd838-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="bd838-190">Takie podejście do utrzymywania bazy danych w synchronizacji z modelem danych działa dobrze, dopóki nie wdrożysz aplikacji w procesach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="bd838-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="bd838-191">Gdy aplikacja jest uruchomiona w produkcji, zwykle przechowuje dane, które muszą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="bd838-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="bd838-192">Aplikacja nie może rozpocząć się od testowej bazy danych za każdym razem, gdy zostanie wniesiena zmiana (na przykład dodanie nowej kolumny).</span><span class="sxs-lookup"><span data-stu-id="bd838-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="bd838-193">Funkcja Migracji rdzenia EF rozwiązuje ten problem, umożliwiając EF Core zaktualizować schemat bazy danych zamiast tworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="bd838-194">Zamiast upuszczania i ponownego tworzenia bazy danych po zmianie modelu danych, migracje aktualizuje schemat i zachowuje istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="bd838-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="bd838-195">Upuść bazę danych</span><span class="sxs-lookup"><span data-stu-id="bd838-195">Drop the database</span></span>

<span data-ttu-id="bd838-196">Użyj **programu SQL Server Object Explorer** `database drop` (SSOX) lub polecenia:</span><span class="sxs-lookup"><span data-stu-id="bd838-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd838-197">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd838-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd838-198">W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="bd838-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="bd838-199">Uruchom `Get-Help about_EntityFrameworkCore` z PMC, aby uzyskać informacje o pomocy.</span><span class="sxs-lookup"><span data-stu-id="bd838-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd838-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd838-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd838-201">Otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="bd838-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="bd838-202">Folder projektu zawiera plik *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="bd838-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="bd838-203">W oknie polecenia wprowadź następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="bd838-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="bd838-204">Tworzenie migracji początkowej i aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="bd838-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="bd838-205">Skompiluj projekt i utwórz pierwszą migrację.</span><span class="sxs-lookup"><span data-stu-id="bd838-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd838-206">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd838-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd838-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd838-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="bd838-208">Sprawdzanie metod W górę i W dół</span><span class="sxs-lookup"><span data-stu-id="bd838-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="bd838-209">Polecenie EF `migrations add` Core wygenerowało kod do utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="bd838-210">Ten kod migracji znajduje się w pliku *sygnatury czasowej\<migracji>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="bd838-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="bd838-211">Metoda `Up` `InitialCreate` klasy tworzy tabele DB, które odpowiadają zestawom jednostek modelu danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="bd838-212">Metoda `Down` usuwa je, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="bd838-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="bd838-213">Migracje wywołuje `Up` metodę zaimplementowania zmian modelu danych dla migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="bd838-214">Po wprowadzeniu polecenia, aby wycofać aktualizację, `Down` migracje wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="bd838-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="bd838-215">Poprzedni kod dotyczy migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="bd838-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="bd838-216">Ten kod został `migrations add InitialCreate` utworzony po uruchomieniu polecenia.</span><span class="sxs-lookup"><span data-stu-id="bd838-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="bd838-217">Parametr nazwa migracji ("InitialCreate" w przykładzie) jest używany dla nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="bd838-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="bd838-218">Nazwa migracji może być dowolną prawidłową nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="bd838-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="bd838-219">Najlepiej wybrać słowo lub frazę, która podsumowuje to, co jest wykonywane podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="bd838-220">Na przykład migracji, która dodała tabelę działu może być nazywany "AddDepartmentTable."</span><span class="sxs-lookup"><span data-stu-id="bd838-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="bd838-221">Jeśli zostanie utworzona migracja początkowa i baza danych istnieje:</span><span class="sxs-lookup"><span data-stu-id="bd838-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="bd838-222">Generowany jest kod tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="bd838-223">Kod tworzenia bazy danych nie musi być uruchamiany, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="bd838-224">Jeśli kod tworzenia bazy danych jest uruchamiany, nie wprowadza żadnych zmian, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="bd838-225">Gdy aplikacja jest wdrażana w nowym środowisku, kod tworzenia bazy danych musi być uruchomiony w celu utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="bd838-226">Wcześniej baza danych została porzucona i nie istnieje, więc migracje tworzy nowy baz danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="bd838-227">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="bd838-227">The data model snapshot</span></span>

<span data-ttu-id="bd838-228">Migracje tworzą *migawkę* bieżącego schematu bazy danych w *programach Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="bd838-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="bd838-229">Po dodaniu migracji EF określa, co się zmieniło, porównując model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="bd838-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="bd838-230">Aby usunąć migrację, użyj następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="bd838-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd838-231">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd838-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd838-232">Usuń migrację</span><span class="sxs-lookup"><span data-stu-id="bd838-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd838-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd838-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="bd838-234">Aby uzyskać więcej informacji, zobacz [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="bd838-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="bd838-235">Polecenie usuń migracje usuwa migrację i zapewnia poprawne zresetowanie migawki.</span><span class="sxs-lookup"><span data-stu-id="bd838-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="bd838-236">Usuń EnsureCreated i przetestuj aplikację</span><span class="sxs-lookup"><span data-stu-id="bd838-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="bd838-237">Do wczesnego `EnsureCreated` rozwoju, był używany.</span><span class="sxs-lookup"><span data-stu-id="bd838-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="bd838-238">W tym samouczku używane są migracje.</span><span class="sxs-lookup"><span data-stu-id="bd838-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="bd838-239">`EnsureCreated`ma następujące ograniczenia:</span><span class="sxs-lookup"><span data-stu-id="bd838-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="bd838-240">Pomija migracje i tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="bd838-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="bd838-241">Nie tworzy tabeli migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="bd838-242">*Nie* można używać z migracjami.</span><span class="sxs-lookup"><span data-stu-id="bd838-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="bd838-243">Jest przeznaczony do testowania lub szybkiego prototypowania, gdzie baza danych jest często odrzucana i odtwarzana.</span><span class="sxs-lookup"><span data-stu-id="bd838-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="bd838-244">Usuń `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="bd838-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="bd838-245">Uruchom aplikację i sprawdź, czy baza danych jest rozstawiona.</span><span class="sxs-lookup"><span data-stu-id="bd838-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="bd838-246">Sprawdzanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="bd838-246">Inspect the database</span></span>

<span data-ttu-id="bd838-247">Użyj **Eksploratora obiektów programu SQL Server,** aby sprawdzić bazę danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="bd838-248">Zwróć uwagę na `__EFMigrationsHistory` dodanie tabeli.</span><span class="sxs-lookup"><span data-stu-id="bd838-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="bd838-249">Tabela `__EFMigrationsHistory` śledzi, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd838-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="bd838-250">Wyświetl dane w `__EFMigrationsHistory` tabeli, pokazuje jeden wiersz dla pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="bd838-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="bd838-251">Ostatni dziennik w poprzednim przykładzie wyjścia interfejsu wiersza polecenia pokazuje insert instrukcji, która tworzy ten wiersz.</span><span class="sxs-lookup"><span data-stu-id="bd838-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="bd838-252">Uruchom aplikację i sprawdź, czy wszystko działa.</span><span class="sxs-lookup"><span data-stu-id="bd838-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="bd838-253">Stosowanie migracji w produkcji</span><span class="sxs-lookup"><span data-stu-id="bd838-253">Applying migrations in production</span></span>

<span data-ttu-id="bd838-254">Zaleca się, że aplikacje produkcyjne **nie** powinny wywoływać [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bd838-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="bd838-255">`Migrate`nie powinien być wywoływany z aplikacji w farmie serwerów.</span><span class="sxs-lookup"><span data-stu-id="bd838-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="bd838-256">Na przykład jeśli aplikacja została wdrożona w chmurze ze skalą w poziomie (wiele wystąpień aplikacji są uruchomione).</span><span class="sxs-lookup"><span data-stu-id="bd838-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="bd838-257">Migracja bazy danych powinna być wykonywana w ramach wdrażania i w sposób kontrolowany.</span><span class="sxs-lookup"><span data-stu-id="bd838-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="bd838-258">Metody migracji danych produkcyjnych obejmują:</span><span class="sxs-lookup"><span data-stu-id="bd838-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="bd838-259">Używanie migracji do tworzenia skryptów SQL i używania skryptów SQL we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="bd838-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="bd838-260">Uruchamianie `dotnet ef database update` z kontrolowanego środowiska.</span><span class="sxs-lookup"><span data-stu-id="bd838-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="bd838-261">EF Core używa `__MigrationsHistory` tabeli, aby sprawdzić, czy wszelkie migracje muszą być uruchamiane.</span><span class="sxs-lookup"><span data-stu-id="bd838-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="bd838-262">Jeśli baza danych jest aktualna, nie jest uruchamiana migracja.</span><span class="sxs-lookup"><span data-stu-id="bd838-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bd838-263">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="bd838-263">Troubleshooting</span></span>

<span data-ttu-id="bd838-264">Pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="bd838-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="bd838-265">Aplikacja generuje następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="bd838-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="bd838-266">Rozwiązanie: Uruchom`dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="bd838-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="bd838-267">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bd838-267">Additional resources</span></span>

* [<span data-ttu-id="bd838-268">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="bd838-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="bd838-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="bd838-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="bd838-270">Konsola menedżera pakietów (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="bd838-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="bd838-271">[Poprzedni](xref:data/ef-rp/sort-filter-page)
> [następny](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="bd838-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

