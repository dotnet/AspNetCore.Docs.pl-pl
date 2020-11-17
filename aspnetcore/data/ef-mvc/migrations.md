---
title: Samouczek 5. stosowanie migracji do przykładowej firmy Contoso University
description: Część 5 z serii samouczków z uczelnią w firmie Contoso. Użyj funkcji migracji EF Core, aby zarządzać zmianami modelu danych w aplikacji ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: contperfq2
ms.date: 11/13/2020
ms.topic: tutorial
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/migrations
ms.openlocfilehash: ab5be222416e61fcff90c5130ca91ad4a2a5c9b0
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674007"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="d13ae-104">Samouczek: część 5, stosowanie migracji do przykładu z uczelnią contoso</span><span class="sxs-lookup"><span data-stu-id="d13ae-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="d13ae-105">W tym samouczku Zacznij korzystać z funkcji migracji EF Core, aby zarządzać zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="d13ae-106">W kolejnych samouczkach dodasz więcej migracji w miarę zmieniania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="d13ae-107">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d13ae-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d13ae-108">Więcej informacji na temat migracji</span><span class="sxs-lookup"><span data-stu-id="d13ae-108">Learn about migrations</span></span>
> * <span data-ttu-id="d13ae-109">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="d13ae-109">Create an initial migration</span></span>
> * <span data-ttu-id="d13ae-110">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="d13ae-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="d13ae-111">Informacje o migawce modelu danych</span><span class="sxs-lookup"><span data-stu-id="d13ae-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="d13ae-112">Zastosuj migrację</span><span class="sxs-lookup"><span data-stu-id="d13ae-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d13ae-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d13ae-113">Prerequisites</span></span>

* [<span data-ttu-id="d13ae-114">Sortowanie, filtrowanie i stronicowanie</span><span class="sxs-lookup"><span data-stu-id="d13ae-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="d13ae-115">Informacje o migracjach</span><span class="sxs-lookup"><span data-stu-id="d13ae-115">About migrations</span></span>

<span data-ttu-id="d13ae-116">Podczas tworzenia nowej aplikacji model danych jest często zmieniany, a przy każdym zmianie modelu jest on synchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="d13ae-117">Te samouczki zostały rozpoczęte przez skonfigurowanie Entity Framework w celu utworzenia bazy danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="d13ae-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="d13ae-118">Następnie za każdym razem, gdy zmieniasz model danych — Dodaj, Usuń lub Zmień klasy jednostek lub Zmień klasę DbContext — możesz usunąć bazę danych i EF tworzy nową, która jest zgodna z modelem, i wydawaj ją z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="d13ae-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="d13ae-119">Ta metoda zachowania synchronizacji bazy danych z modelem danych działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="d13ae-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="d13ae-120">Gdy aplikacja jest uruchomiona w środowisku produkcyjnym, zazwyczaj przechowuje dane, które mają być zachowane, i nie ma potrzeby utraty wszystkiego przy każdym wprowadzeniu zmiany, takiej jak dodanie nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="d13ae-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="d13ae-121">Funkcja migracji EF Core rozwiązuje ten problem przez włączenie programu EF w celu zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="d13ae-122">Aby móc korzystać z migracji, można użyć **konsoli Menedżera pakietów** (PMC) lub interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="d13ae-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="d13ae-123">W tych samouczkach pokazano, jak używać poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="d13ae-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="d13ae-124">Informacje na temat obiektu PMC są na [końcu tego samouczka](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d13ae-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="d13ae-125">Porzuć bazę danych</span><span class="sxs-lookup"><span data-stu-id="d13ae-125">Drop the database</span></span>

<span data-ttu-id="d13ae-126">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-126">Delete the database.</span></span> <span data-ttu-id="d13ae-127">Użyj **Eksplorator obiektów SQL Server** (SSOX) lub `database drop` interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="d13ae-127">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

<span data-ttu-id="d13ae-128">W poniższej sekcji opisano sposób uruchamiania poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="d13ae-128">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="d13ae-129">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="d13ae-129">Create an initial migration</span></span>

<span data-ttu-id="d13ae-130">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="d13ae-130">Save your changes and build the project.</span></span> <span data-ttu-id="d13ae-131">Następnie otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="d13ae-131">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="d13ae-132">Oto szybka metoda:</span><span class="sxs-lookup"><span data-stu-id="d13ae-132">Here's a quick way to do that:</span></span>

* <span data-ttu-id="d13ae-133">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Otwórz folder w Eksploratorze plików** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="d13ae-133">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Otwórz w elemencie menu Eksploratora plików](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="d13ae-135">Wprowadź ciąg "cmd" na pasku adresu i naciśnij klawisz ENTER.</span><span class="sxs-lookup"><span data-stu-id="d13ae-135">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Otwórz okno polecenia](migrations/_static/open-command-window.png)

<span data-ttu-id="d13ae-137">Wprowadź następujące polecenie w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="d13ae-137">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="d13ae-138">`dotnet tool install --global dotnet-ef` Program `dotnet ef` jest instalowany jako [Narzędzie globalne](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="d13ae-138">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="d13ae-139">W poprzednich poleceniach są wyświetlane dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="d13ae-139">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="d13ae-140">Jeśli zostanie wyświetlony komunikat o błędzie "*nie można uzyskać dostępu do pliku... ContosoUniversity.dll, ponieważ jest on używany przez inny proces*", znajdź ikonę IIS Express na pasku zadań systemu Windows, a następnie kliknij ją prawym przyciskiem myszy, a następnie kliknij pozycję **ContosoUniversity > Zatrzymaj witrynę**.</span><span class="sxs-lookup"><span data-stu-id="d13ae-140">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="d13ae-141">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="d13ae-141">Examine Up and Down methods</span></span>

<span data-ttu-id="d13ae-142">Po wykonaniu `migrations add` polecenia EF wygenerowało kod, który spowoduje utworzenie bazy danych od podstaw.</span><span class="sxs-lookup"><span data-stu-id="d13ae-142">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="d13ae-143">Ten kod znajduje się w folderze *migrations* w pliku o nazwie *\<timestamp> _InitialCreate. cs*.</span><span class="sxs-lookup"><span data-stu-id="d13ae-143">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="d13ae-144">`Up`Metoda `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych, a `Down` Metoda usuwa je, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d13ae-144">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="d13ae-145">Migracja wywołuje `Up` metodę w celu zaimplementowania zmian modelu danych dla migracji.</span><span class="sxs-lookup"><span data-stu-id="d13ae-145">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="d13ae-146">Po wprowadzeniu polecenia w celu wycofania aktualizacji migracja wywołuje `Down` metodę.</span><span class="sxs-lookup"><span data-stu-id="d13ae-146">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="d13ae-147">Ten kod jest przeznaczony dla początkowej migracji, która została utworzona po wprowadzeniu `migrations add InitialCreate` polecenia.</span><span class="sxs-lookup"><span data-stu-id="d13ae-147">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="d13ae-148">Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku i może być dowolnie wybrany.</span><span class="sxs-lookup"><span data-stu-id="d13ae-148">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="d13ae-149">Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="d13ae-149">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="d13ae-150">Można na przykład nazwać migrację do nowszej wersji "adddepartments".</span><span class="sxs-lookup"><span data-stu-id="d13ae-150">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="d13ae-151">W przypadku utworzenia początkowej migracji, gdy baza danych już istnieje, kod tworzenia bazy danych jest generowany, ale nie musi działać, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-151">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="d13ae-152">Po wdrożeniu aplikacji w innym środowisku, w którym baza danych jeszcze nie istnieje, ten kod zostanie uruchomiony w celu utworzenia bazy danych, więc dobrym pomysłem jest przetestowanie go w pierwszej kolejności.</span><span class="sxs-lookup"><span data-stu-id="d13ae-152">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="d13ae-153">Z tego powodu zmieniono nazwę bazy danych w parametrach połączenia wcześniej — tak, aby migracje mogły utworzyć nową od podstaw.</span><span class="sxs-lookup"><span data-stu-id="d13ae-153">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="d13ae-154">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="d13ae-154">The data model snapshot</span></span>

<span data-ttu-id="d13ae-155">Migracja tworzy *migawkę* bieżącego schematu bazy danych w *migracji/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="d13ae-155">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="d13ae-156">Po dodaniu migracji, EF określa, co zmieniło się, porównując model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="d13ae-156">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="d13ae-157">Aby usunąć migrację, użyj polecenia [migracji programu dotnet EF Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) .</span><span class="sxs-lookup"><span data-stu-id="d13ae-157">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="d13ae-158">`dotnet ef migrations remove` usuwa migrację i gwarantuje, że migawka zostanie prawidłowo zresetowana.</span><span class="sxs-lookup"><span data-stu-id="d13ae-158">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="d13ae-159">Jeśli `dotnet ef migrations remove` to się nie powiedzie, użyj, `dotnet ef migrations remove -v` Aby uzyskać więcej informacji na temat błędu.</span><span class="sxs-lookup"><span data-stu-id="d13ae-159">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="d13ae-160">Aby uzyskać więcej informacji na temat sposobu użycia pliku migawek, zobacz [EF Core migracji w środowiskach zespołu](/ef/core/managing-schemas/migrations/teams) .</span><span class="sxs-lookup"><span data-stu-id="d13ae-160">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="d13ae-161">Zastosuj migrację</span><span class="sxs-lookup"><span data-stu-id="d13ae-161">Apply the migration</span></span>

<span data-ttu-id="d13ae-162">W oknie wiersza polecenia wprowadź następujące polecenie, aby utworzyć bazę danych i tabele.</span><span class="sxs-lookup"><span data-stu-id="d13ae-162">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="d13ae-163">Dane wyjściowe polecenia są podobne do `migrations add` polecenia, z tą różnicą, że są wyświetlane dzienniki poleceń SQL, które konfigurują bazę danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-163">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="d13ae-164">Większość dzienników zostanie pominiętych w następujących przykładowych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-164">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="d13ae-165">Jeśli wolisz, aby nie wyświetlać tego poziomu szczegółów w komunikatach dziennika, możesz zmienić poziom rejestrowania w *appsettings.Development.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="d13ae-165">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="d13ae-166">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="d13ae-166">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="d13ae-167">Użyj **Eksplorator obiektów SQL Server** , aby sprawdzić bazę danych zgodnie z pierwszym samouczkiem.</span><span class="sxs-lookup"><span data-stu-id="d13ae-167">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="d13ae-168">Zauważysz dodanie \_ \_ tabeli EFMigrationsHistory, która śledzi, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-168">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="d13ae-169">Wyświetl dane w tej tabeli i po pierwszej migracji zobaczysz jeden wiersz.</span><span class="sxs-lookup"><span data-stu-id="d13ae-169">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="d13ae-170">(Ostatni dziennik w poprzednim przykładzie danych wyjściowych interfejsu wiersza polecenia przedstawia instrukcję INSERT, która tworzy ten wiersz).</span><span class="sxs-lookup"><span data-stu-id="d13ae-170">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="d13ae-171">Uruchom aplikację, aby upewnić się, że wszystko nadal działa tak samo jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="d13ae-171">Run the application to verify that everything still works the same as before.</span></span>

![Strona indeksu uczniów](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="d13ae-173">Porównanie interfejsu wiersza polecenia i kryteriów PMC</span><span class="sxs-lookup"><span data-stu-id="d13ae-173">Compare CLI and PMC</span></span>

<span data-ttu-id="d13ae-174">Narzędzia EF do zarządzania migracjami są dostępne z poleceń interfejs wiersza polecenia platformy .NET Core lub z poleceń cmdlet programu PowerShell w oknie **konsola Menedżera pakietów** programu Visual Studio (PMC).</span><span class="sxs-lookup"><span data-stu-id="d13ae-174">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="d13ae-175">W tym samouczku pokazano, jak używać interfejsu wiersza polecenia, ale możesz użyć kryterium PMC, jeśli wolisz.</span><span class="sxs-lookup"><span data-stu-id="d13ae-175">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="d13ae-176">Polecenia EF dla poleceń PMC znajdują się w pakiecie [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) .</span><span class="sxs-lookup"><span data-stu-id="d13ae-176">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="d13ae-177">Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie trzeba dodawać odwołania do pakietu, jeśli aplikacja zawiera odwołanie do pakietu `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="d13ae-177">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="d13ae-178">**Ważne:** To nie jest ten sam pakiet, który jest instalowany dla interfejsu wiersza polecenia, edytując plik *csproj* .</span><span class="sxs-lookup"><span data-stu-id="d13ae-178">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="d13ae-179">Nazwa tego elementu zostanie zakończona, w `Tools` przeciwieństwie do nazwy pakietu interfejsu wiersza polecenia kończącego się na `Tools.DotNet` .</span><span class="sxs-lookup"><span data-stu-id="d13ae-179">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="d13ae-180">Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="d13ae-180">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="d13ae-181">Aby uzyskać więcej informacji na temat poleceń PMC, zobacz [konsola Menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="d13ae-181">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="d13ae-182">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="d13ae-182">Get the code</span></span>

[<span data-ttu-id="d13ae-183">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="d13ae-183">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="d13ae-184">Następny krok</span><span class="sxs-lookup"><span data-stu-id="d13ae-184">Next step</span></span>

<span data-ttu-id="d13ae-185">Przejdź do następnego samouczka, aby rozpocząć bardziej zaawansowane tematy dotyczące rozszerzania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="d13ae-185">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="d13ae-186">W sposób tworzenia i stosowania dodatkowych migracji.</span><span class="sxs-lookup"><span data-stu-id="d13ae-186">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d13ae-187">Tworzenie i stosowanie dodatkowych migracji</span><span class="sxs-lookup"><span data-stu-id="d13ae-187">Create and apply additional migrations</span></span>](complex-data-model.md)
