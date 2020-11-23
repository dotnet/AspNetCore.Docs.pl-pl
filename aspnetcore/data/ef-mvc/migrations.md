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
ms.openlocfilehash: 25ada6e3a210ed830514e69da7d5212264b4b91e
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417568"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="fa5de-104">Samouczek: część 5, stosowanie migracji do przykładu z uczelnią contoso</span><span class="sxs-lookup"><span data-stu-id="fa5de-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="fa5de-105">W tym samouczku Zacznij korzystać z funkcji migracji EF Core, aby zarządzać zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="fa5de-106">W kolejnych samouczkach dodasz więcej migracji w miarę zmieniania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="fa5de-107">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="fa5de-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa5de-108">Więcej informacji na temat migracji</span><span class="sxs-lookup"><span data-stu-id="fa5de-108">Learn about migrations</span></span>
> * <span data-ttu-id="fa5de-109">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="fa5de-109">Create an initial migration</span></span>
> * <span data-ttu-id="fa5de-110">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="fa5de-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="fa5de-111">Informacje o migawce modelu danych</span><span class="sxs-lookup"><span data-stu-id="fa5de-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="fa5de-112">Zastosuj migrację</span><span class="sxs-lookup"><span data-stu-id="fa5de-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa5de-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="fa5de-113">Prerequisites</span></span>

* [<span data-ttu-id="fa5de-114">Sortowanie, filtrowanie i stronicowanie</span><span class="sxs-lookup"><span data-stu-id="fa5de-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="fa5de-115">Informacje o migracjach</span><span class="sxs-lookup"><span data-stu-id="fa5de-115">About migrations</span></span>

<span data-ttu-id="fa5de-116">Podczas tworzenia nowej aplikacji model danych jest często zmieniany, a przy każdym zmianie modelu jest on synchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="fa5de-117">Te samouczki zostały rozpoczęte przez skonfigurowanie Entity Framework w celu utworzenia bazy danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="fa5de-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="fa5de-118">Następnie za każdym razem, gdy zmieniasz model danych — Dodaj, Usuń lub Zmień klasy jednostek lub Zmień klasę DbContext — możesz usunąć bazę danych i EF tworzy nową, która jest zgodna z modelem, i wydawaj ją z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="fa5de-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="fa5de-119">Ta metoda zachowania synchronizacji bazy danych z modelem danych działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="fa5de-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="fa5de-120">Gdy aplikacja jest uruchomiona w środowisku produkcyjnym, zazwyczaj przechowuje dane, które mają być zachowane, i nie ma potrzeby utraty wszystkiego przy każdym wprowadzeniu zmiany, takiej jak dodanie nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="fa5de-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="fa5de-121">Funkcja migracji EF Core rozwiązuje ten problem przez włączenie programu EF w celu zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="fa5de-122">Aby móc korzystać z migracji, można użyć **konsoli Menedżera pakietów** (PMC) lub interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="fa5de-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="fa5de-123">W tych samouczkach pokazano, jak używać poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="fa5de-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="fa5de-124">Informacje na temat obiektu PMC są na [końcu tego samouczka](#pmc).</span><span class="sxs-lookup"><span data-stu-id="fa5de-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="fa5de-125">Porzuć bazę danych</span><span class="sxs-lookup"><span data-stu-id="fa5de-125">Drop the database</span></span>

<span data-ttu-id="fa5de-126">Zainstaluj EF Core narzędzia jako [Narzędzie globalne](/ef/core/miscellaneous/cli/dotnet) i Usuń bazę danych:</span><span class="sxs-lookup"><span data-stu-id="fa5de-126">Install EF Core tools as a [global tool](/ef/core/miscellaneous/cli/dotnet) and delete the database:</span></span>

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

<span data-ttu-id="fa5de-127">W poniższej sekcji opisano sposób uruchamiania poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="fa5de-127">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="fa5de-128">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="fa5de-128">Create an initial migration</span></span>

<span data-ttu-id="fa5de-129">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="fa5de-129">Save your changes and build the project.</span></span> <span data-ttu-id="fa5de-130">Następnie otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="fa5de-130">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="fa5de-131">Oto szybka metoda:</span><span class="sxs-lookup"><span data-stu-id="fa5de-131">Here's a quick way to do that:</span></span>

* <span data-ttu-id="fa5de-132">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Otwórz folder w Eksploratorze plików** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="fa5de-132">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Otwórz w elemencie menu Eksploratora plików](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="fa5de-134">Wprowadź ciąg "cmd" na pasku adresu i naciśnij klawisz ENTER.</span><span class="sxs-lookup"><span data-stu-id="fa5de-134">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Otwórz okno polecenia](migrations/_static/open-command-window.png)

<span data-ttu-id="fa5de-136">Wprowadź następujące polecenie w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="fa5de-136">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="fa5de-137">W poprzednich poleceniach są wyświetlane dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="fa5de-137">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="fa5de-138">Jeśli zostanie wyświetlony komunikat o błędzie "*nie można uzyskać dostępu do pliku... ContosoUniversity.dll, ponieważ jest on używany przez inny proces*", znajdź ikonę IIS Express na pasku zadań systemu Windows, a następnie kliknij ją prawym przyciskiem myszy, a następnie kliknij pozycję **ContosoUniversity > Zatrzymaj witrynę**.</span><span class="sxs-lookup"><span data-stu-id="fa5de-138">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="fa5de-139">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="fa5de-139">Examine Up and Down methods</span></span>

<span data-ttu-id="fa5de-140">Po wykonaniu `migrations add` polecenia EF wygenerowało kod, który spowoduje utworzenie bazy danych od podstaw.</span><span class="sxs-lookup"><span data-stu-id="fa5de-140">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="fa5de-141">Ten kod znajduje się w folderze *migrations* w pliku o nazwie *\<timestamp> _InitialCreate. cs*.</span><span class="sxs-lookup"><span data-stu-id="fa5de-141">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="fa5de-142">`Up`Metoda `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych, a `Down` Metoda usuwa je, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="fa5de-142">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="fa5de-143">Migracja wywołuje `Up` metodę w celu zaimplementowania zmian modelu danych dla migracji.</span><span class="sxs-lookup"><span data-stu-id="fa5de-143">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="fa5de-144">Po wprowadzeniu polecenia w celu wycofania aktualizacji migracja wywołuje `Down` metodę.</span><span class="sxs-lookup"><span data-stu-id="fa5de-144">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="fa5de-145">Ten kod jest przeznaczony dla początkowej migracji, która została utworzona po wprowadzeniu `migrations add InitialCreate` polecenia.</span><span class="sxs-lookup"><span data-stu-id="fa5de-145">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="fa5de-146">Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku i może być dowolnie wybrany.</span><span class="sxs-lookup"><span data-stu-id="fa5de-146">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="fa5de-147">Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="fa5de-147">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="fa5de-148">Można na przykład nazwać migrację do nowszej wersji "adddepartments".</span><span class="sxs-lookup"><span data-stu-id="fa5de-148">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="fa5de-149">W przypadku utworzenia początkowej migracji, gdy baza danych już istnieje, kod tworzenia bazy danych jest generowany, ale nie musi działać, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-149">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="fa5de-150">Po wdrożeniu aplikacji w innym środowisku, w którym baza danych jeszcze nie istnieje, ten kod zostanie uruchomiony w celu utworzenia bazy danych, więc dobrym pomysłem jest przetestowanie go w pierwszej kolejności.</span><span class="sxs-lookup"><span data-stu-id="fa5de-150">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="fa5de-151">Z tego powodu zmieniono nazwę bazy danych w parametrach połączenia wcześniej — tak, aby migracje mogły utworzyć nową od podstaw.</span><span class="sxs-lookup"><span data-stu-id="fa5de-151">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="fa5de-152">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="fa5de-152">The data model snapshot</span></span>

<span data-ttu-id="fa5de-153">Migracja tworzy *migawkę* bieżącego schematu bazy danych w *migracji/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="fa5de-153">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="fa5de-154">Po dodaniu migracji, EF określa, co zmieniło się, porównując model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="fa5de-154">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="fa5de-155">Aby usunąć migrację, użyj polecenia [migracji programu dotnet EF Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) .</span><span class="sxs-lookup"><span data-stu-id="fa5de-155">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="fa5de-156">`dotnet ef migrations remove` usuwa migrację i gwarantuje, że migawka zostanie prawidłowo zresetowana.</span><span class="sxs-lookup"><span data-stu-id="fa5de-156">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="fa5de-157">Jeśli `dotnet ef migrations remove` to się nie powiedzie, użyj, `dotnet ef migrations remove -v` Aby uzyskać więcej informacji na temat błędu.</span><span class="sxs-lookup"><span data-stu-id="fa5de-157">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="fa5de-158">Aby uzyskać więcej informacji na temat sposobu użycia pliku migawek, zobacz [EF Core migracji w środowiskach zespołu](/ef/core/managing-schemas/migrations/teams) .</span><span class="sxs-lookup"><span data-stu-id="fa5de-158">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="fa5de-159">Zastosuj migrację</span><span class="sxs-lookup"><span data-stu-id="fa5de-159">Apply the migration</span></span>

<span data-ttu-id="fa5de-160">W oknie wiersza polecenia wprowadź następujące polecenie, aby utworzyć bazę danych i tabele.</span><span class="sxs-lookup"><span data-stu-id="fa5de-160">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="fa5de-161">Dane wyjściowe polecenia są podobne do `migrations add` polecenia, z tą różnicą, że są wyświetlane dzienniki poleceń SQL, które konfigurują bazę danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-161">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="fa5de-162">Większość dzienników zostanie pominiętych w następujących przykładowych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-162">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="fa5de-163">Jeśli wolisz, aby nie wyświetlać tego poziomu szczegółów w komunikatach dziennika, możesz zmienić poziom rejestrowania w *appsettings.Development.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="fa5de-163">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="fa5de-164">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="fa5de-164">For more information, see <xref:fundamentals/logging/index>.</span></span>

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

<span data-ttu-id="fa5de-165">Użyj **Eksplorator obiektów SQL Server** , aby sprawdzić bazę danych zgodnie z pierwszym samouczkiem.</span><span class="sxs-lookup"><span data-stu-id="fa5de-165">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="fa5de-166">Zauważysz dodanie \_ \_ tabeli EFMigrationsHistory, która śledzi, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-166">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="fa5de-167">Wyświetl dane w tej tabeli i po pierwszej migracji zobaczysz jeden wiersz.</span><span class="sxs-lookup"><span data-stu-id="fa5de-167">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="fa5de-168">(Ostatni dziennik w poprzednim przykładzie danych wyjściowych interfejsu wiersza polecenia przedstawia instrukcję INSERT, która tworzy ten wiersz).</span><span class="sxs-lookup"><span data-stu-id="fa5de-168">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="fa5de-169">Uruchom aplikację, aby upewnić się, że wszystko nadal działa tak samo jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="fa5de-169">Run the application to verify that everything still works the same as before.</span></span>

![Strona indeksu uczniów](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="fa5de-171">Porównanie interfejsu wiersza polecenia i kryteriów PMC</span><span class="sxs-lookup"><span data-stu-id="fa5de-171">Compare CLI and PMC</span></span>

<span data-ttu-id="fa5de-172">Narzędzia EF do zarządzania migracjami są dostępne z poleceń interfejs wiersza polecenia platformy .NET Core lub z poleceń cmdlet programu PowerShell w oknie **konsola Menedżera pakietów** programu Visual Studio (PMC).</span><span class="sxs-lookup"><span data-stu-id="fa5de-172">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="fa5de-173">W tym samouczku pokazano, jak używać interfejsu wiersza polecenia, ale możesz użyć kryterium PMC, jeśli wolisz.</span><span class="sxs-lookup"><span data-stu-id="fa5de-173">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="fa5de-174">Polecenia EF dla poleceń PMC znajdują się w pakiecie [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) .</span><span class="sxs-lookup"><span data-stu-id="fa5de-174">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="fa5de-175">Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie trzeba dodawać odwołania do pakietu, jeśli aplikacja zawiera odwołanie do pakietu `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="fa5de-175">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="fa5de-176">**Ważne:** To nie jest ten sam pakiet, który jest instalowany dla interfejsu wiersza polecenia, edytując plik *csproj* .</span><span class="sxs-lookup"><span data-stu-id="fa5de-176">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="fa5de-177">Nazwa tego elementu zostanie zakończona, w `Tools` przeciwieństwie do nazwy pakietu interfejsu wiersza polecenia kończącego się na `Tools.DotNet` .</span><span class="sxs-lookup"><span data-stu-id="fa5de-177">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="fa5de-178">Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="fa5de-178">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="fa5de-179">Aby uzyskać więcej informacji na temat poleceń PMC, zobacz [konsola Menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="fa5de-179">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="fa5de-180">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="fa5de-180">Get the code</span></span>

[<span data-ttu-id="fa5de-181">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="fa5de-181">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="fa5de-182">Następny krok</span><span class="sxs-lookup"><span data-stu-id="fa5de-182">Next step</span></span>

<span data-ttu-id="fa5de-183">Przejdź do następnego samouczka, aby rozpocząć bardziej zaawansowane tematy dotyczące rozszerzania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="fa5de-183">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="fa5de-184">W sposób tworzenia i stosowania dodatkowych migracji.</span><span class="sxs-lookup"><span data-stu-id="fa5de-184">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="fa5de-185">Tworzenie i stosowanie dodatkowych migracji</span><span class="sxs-lookup"><span data-stu-id="fa5de-185">Create and apply additional migrations</span></span>](complex-data-model.md)
