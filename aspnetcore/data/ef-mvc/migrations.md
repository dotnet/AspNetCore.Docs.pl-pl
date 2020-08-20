---
title: 'Samouczek: Używanie funkcji migracji ASP.NET MVC z EF Core'
description: W tym samouczku rozpocznie się korzystanie z funkcji migracji EF Core na potrzeby zarządzania zmianami modelu danych w aplikacji ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
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
ms.openlocfilehash: 058d59834dc53b6280b8e4ff285a1860e90e257c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629447"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="dc2e9-103">Samouczek: Używanie funkcji migracji ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="dc2e9-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="dc2e9-104">W tym samouczku Zacznij korzystać z funkcji migracji EF Core, aby zarządzać zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="dc2e9-105">W kolejnych samouczkach dodasz więcej migracji w miarę zmieniania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="dc2e9-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dc2e9-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dc2e9-107">Więcej informacji na temat migracji</span><span class="sxs-lookup"><span data-stu-id="dc2e9-107">Learn about migrations</span></span>
> * <span data-ttu-id="dc2e9-108">Zmień parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="dc2e9-108">Change the connection string</span></span>
> * <span data-ttu-id="dc2e9-109">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="dc2e9-109">Create an initial migration</span></span>
> * <span data-ttu-id="dc2e9-110">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="dc2e9-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="dc2e9-111">Informacje o migawce modelu danych</span><span class="sxs-lookup"><span data-stu-id="dc2e9-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="dc2e9-112">Zastosuj migrację</span><span class="sxs-lookup"><span data-stu-id="dc2e9-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc2e9-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="dc2e9-113">Prerequisites</span></span>

* [<span data-ttu-id="dc2e9-114">Sortowanie, filtrowanie i stronicowanie</span><span class="sxs-lookup"><span data-stu-id="dc2e9-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="dc2e9-115">Informacje o migracjach</span><span class="sxs-lookup"><span data-stu-id="dc2e9-115">About migrations</span></span>

<span data-ttu-id="dc2e9-116">Podczas tworzenia nowej aplikacji model danych jest często zmieniany, a przy każdym zmianie modelu jest on synchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="dc2e9-117">Te samouczki zostały rozpoczęte przez skonfigurowanie Entity Framework w celu utworzenia bazy danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="dc2e9-118">Następnie za każdym razem, gdy zmieniasz model danych — Dodaj, Usuń lub Zmień klasy jednostek lub Zmień klasę DbContext — możesz usunąć bazę danych i EF tworzy nową, która jest zgodna z modelem, i wydawaj ją z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="dc2e9-119">Ta metoda zachowania synchronizacji bazy danych z modelem danych działa prawidłowo, dopóki aplikacja nie zostanie wdrożona w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="dc2e9-120">Gdy aplikacja jest uruchomiona w środowisku produkcyjnym, zazwyczaj przechowuje dane, które mają być zachowane, i nie ma potrzeby utraty wszystkiego przy każdym wprowadzeniu zmiany, takiej jak dodanie nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="dc2e9-121">Funkcja migracji EF Core rozwiązuje ten problem przez włączenie programu EF w celu zaktualizowania schematu bazy danych zamiast tworzenia nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="dc2e9-122">Aby móc korzystać z migracji, można użyć **konsoli Menedżera pakietów** (PMC) lub interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="dc2e9-123">W tych samouczkach pokazano, jak używać poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="dc2e9-124">Informacje na temat obiektu PMC są na [końcu tego samouczka](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dc2e9-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="dc2e9-125">Zmień parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="dc2e9-125">Change the connection string</span></span>

<span data-ttu-id="dc2e9-126">W *appsettings.jsna* pliku Zmień nazwę bazy danych w parametrach połączenia na ContosoUniversity2 lub inną nazwę, która nie została użyta na komputerze, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="dc2e9-127">Ta zmiana konfiguruje projekt w taki sposób, aby podczas pierwszej migracji utworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="dc2e9-128">Nie jest to wymagane, aby rozpocząć migrację, ale zobaczysz później, dlaczego jest to dobry pomysł.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="dc2e9-129">Alternatywą dla zmiany nazwy bazy danych jest usunięcie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="dc2e9-130">Użyj **Eksplorator obiektów SQL Server** (SSOX) lub `database drop` interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="dc2e9-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="dc2e9-131">W poniższej sekcji opisano sposób uruchamiania poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="dc2e9-132">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="dc2e9-132">Create an initial migration</span></span>

<span data-ttu-id="dc2e9-133">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-133">Save your changes and build the project.</span></span> <span data-ttu-id="dc2e9-134">Następnie otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="dc2e9-135">Oto szybka metoda:</span><span class="sxs-lookup"><span data-stu-id="dc2e9-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="dc2e9-136">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Otwórz folder w Eksploratorze plików** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Otwórz w elemencie menu Eksploratora plików](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="dc2e9-138">Wprowadź ciąg "cmd" na pasku adresu i naciśnij klawisz ENTER.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Otwórz okno polecenia](migrations/_static/open-command-window.png)

<span data-ttu-id="dc2e9-140">Wprowadź następujące polecenie w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="dc2e9-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="dc2e9-141">`dotnet tool install --global dotnet-ef` Program `dotnet ef` jest instalowany jako [Narzędzie globalne](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="dc2e9-141">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="dc2e9-142">W poprzednich poleceniach są wyświetlane dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="dc2e9-142">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="dc2e9-143">Jeśli zostanie wyświetlony komunikat o błędzie "*nie można uzyskać dostępu do pliku... ContosoUniversity.dll, ponieważ jest on używany przez inny proces*", znajdź ikonę IIS Express na pasku zadań systemu Windows, a następnie kliknij ją prawym przyciskiem myszy, a następnie kliknij pozycję **ContosoUniversity > Zatrzymaj witrynę**.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="dc2e9-144">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="dc2e9-144">Examine Up and Down methods</span></span>

<span data-ttu-id="dc2e9-145">Po wykonaniu `migrations add` polecenia EF wygenerowało kod, który spowoduje utworzenie bazy danych od podstaw.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="dc2e9-146">Ten kod znajduje się w folderze *migrations* w pliku o nazwie \* \<timestamp> _InitialCreate. cs\*.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="dc2e9-147">`Up`Metoda `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom jednostek modelu danych, a `Down` Metoda usuwa je, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="dc2e9-148">Migracja wywołuje `Up` metodę w celu zaimplementowania zmian modelu danych dla migracji.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="dc2e9-149">Po wprowadzeniu polecenia w celu wycofania aktualizacji migracja wywołuje `Down` metodę.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="dc2e9-150">Ten kod jest przeznaczony dla początkowej migracji, która została utworzona po wprowadzeniu `migrations add InitialCreate` polecenia.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="dc2e9-151">Parametr name migracji ("InitialCreate" w przykładzie) jest używany jako nazwa pliku i może być dowolnie wybrany.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="dc2e9-152">Najlepiej wybrać słowo lub frazę, która podsumowuje zawartość wykonywaną podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="dc2e9-153">Można na przykład nazwać migrację do nowszej wersji "adddepartments".</span><span class="sxs-lookup"><span data-stu-id="dc2e9-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="dc2e9-154">W przypadku utworzenia początkowej migracji, gdy baza danych już istnieje, kod tworzenia bazy danych jest generowany, ale nie musi działać, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="dc2e9-155">Po wdrożeniu aplikacji w innym środowisku, w którym baza danych jeszcze nie istnieje, ten kod zostanie uruchomiony w celu utworzenia bazy danych, więc dobrym pomysłem jest przetestowanie go w pierwszej kolejności.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="dc2e9-156">Z tego powodu zmieniono nazwę bazy danych w parametrach połączenia wcześniej — tak, aby migracje mogły utworzyć nową od podstaw.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="dc2e9-157">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="dc2e9-157">The data model snapshot</span></span>

<span data-ttu-id="dc2e9-158">Migracja tworzy *migawkę* bieżącego schematu bazy danych w *migracji/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="dc2e9-159">Po dodaniu migracji, EF określa, co zmieniło się, porównując model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="dc2e9-160">Aby usunąć migrację, użyj polecenia [migracji programu dotnet EF Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) .</span><span class="sxs-lookup"><span data-stu-id="dc2e9-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="dc2e9-161">`dotnet ef migrations remove` usuwa migrację i gwarantuje, że migawka zostanie prawidłowo zresetowana.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="dc2e9-162">Jeśli `dotnet ef migrations remove` to się nie powiedzie, użyj, `dotnet ef migrations remove -v` Aby uzyskać więcej informacji na temat błędu.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="dc2e9-163">Aby uzyskać więcej informacji na temat sposobu użycia pliku migawek, zobacz [EF Core migracji w środowiskach zespołu](/ef/core/managing-schemas/migrations/teams) .</span><span class="sxs-lookup"><span data-stu-id="dc2e9-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="dc2e9-164">Zastosuj migrację</span><span class="sxs-lookup"><span data-stu-id="dc2e9-164">Apply the migration</span></span>

<span data-ttu-id="dc2e9-165">W oknie wiersza polecenia wprowadź następujące polecenie, aby utworzyć bazę danych i tabele.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="dc2e9-166">Dane wyjściowe polecenia są podobne do `migrations add` polecenia, z tą różnicą, że są wyświetlane dzienniki poleceń SQL, które konfigurują bazę danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="dc2e9-167">Większość dzienników zostanie pominiętych w następujących przykładowych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="dc2e9-168">Jeśli wolisz, aby nie wyświetlać tego poziomu szczegółów w komunikatach dziennika, możesz zmienić poziom rejestrowania w *appsettings.Development.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="dc2e9-169">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
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
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="dc2e9-170">Użyj **Eksplorator obiektów SQL Server** , aby sprawdzić bazę danych zgodnie z pierwszym samouczkiem.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="dc2e9-171">Zauważysz dodanie \_ \_ tabeli EFMigrationsHistory, która śledzi, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="dc2e9-172">Wyświetl dane w tej tabeli i po pierwszej migracji zobaczysz jeden wiersz.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="dc2e9-173">(Ostatni dziennik w poprzednim przykładzie danych wyjściowych interfejsu wiersza polecenia przedstawia instrukcję INSERT, która tworzy ten wiersz).</span><span class="sxs-lookup"><span data-stu-id="dc2e9-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="dc2e9-174">Uruchom aplikację, aby upewnić się, że wszystko nadal działa tak samo jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-174">Run the application to verify that everything still works the same as before.</span></span>

![Strona indeksu uczniów](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="dc2e9-176">Porównanie interfejsu wiersza polecenia i kryteriów PMC</span><span class="sxs-lookup"><span data-stu-id="dc2e9-176">Compare CLI and PMC</span></span>

<span data-ttu-id="dc2e9-177">Narzędzia EF do zarządzania migracjami są dostępne z poleceń interfejs wiersza polecenia platformy .NET Core lub z poleceń cmdlet programu PowerShell w oknie **konsola Menedżera pakietów** programu Visual Studio (PMC).</span><span class="sxs-lookup"><span data-stu-id="dc2e9-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="dc2e9-178">W tym samouczku pokazano, jak używać interfejsu wiersza polecenia, ale możesz użyć kryterium PMC, jeśli wolisz.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="dc2e9-179">Polecenia EF dla poleceń PMC znajdują się w pakiecie [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) .</span><span class="sxs-lookup"><span data-stu-id="dc2e9-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="dc2e9-180">Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie trzeba dodawać odwołania do pakietu, jeśli aplikacja zawiera odwołanie do pakietu `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="dc2e9-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="dc2e9-181">**Ważne:** To nie jest ten sam pakiet, który jest instalowany dla interfejsu wiersza polecenia, edytując plik *csproj* .</span><span class="sxs-lookup"><span data-stu-id="dc2e9-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="dc2e9-182">Nazwa tego elementu zostanie zakończona, w `Tools` przeciwieństwie do nazwy pakietu interfejsu wiersza polecenia kończącego się na `Tools.DotNet` .</span><span class="sxs-lookup"><span data-stu-id="dc2e9-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="dc2e9-183">Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [interfejs wiersza polecenia platformy .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="dc2e9-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="dc2e9-184">Aby uzyskać więcej informacji na temat poleceń PMC, zobacz [konsola Menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="dc2e9-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="dc2e9-185">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="dc2e9-185">Get the code</span></span>

[<span data-ttu-id="dc2e9-186">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="dc2e9-187">Następny krok</span><span class="sxs-lookup"><span data-stu-id="dc2e9-187">Next step</span></span>

<span data-ttu-id="dc2e9-188">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="dc2e9-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dc2e9-189">Zapoznaj się z migracjami</span><span class="sxs-lookup"><span data-stu-id="dc2e9-189">Learned about migrations</span></span>
> * <span data-ttu-id="dc2e9-190">Zapoznaj się z pakietami migracji NuGet</span><span class="sxs-lookup"><span data-stu-id="dc2e9-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="dc2e9-191">Zmieniono parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="dc2e9-191">Changed the connection string</span></span>
> * <span data-ttu-id="dc2e9-192">Tworzenie początkowej migracji</span><span class="sxs-lookup"><span data-stu-id="dc2e9-192">Created an initial migration</span></span>
> * <span data-ttu-id="dc2e9-193">Badanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="dc2e9-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="dc2e9-194">Informacje o migawce modelu danych</span><span class="sxs-lookup"><span data-stu-id="dc2e9-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="dc2e9-195">Zastosowano migrację</span><span class="sxs-lookup"><span data-stu-id="dc2e9-195">Applied the migration</span></span>

<span data-ttu-id="dc2e9-196">Przejdź do następnego samouczka, aby rozpocząć bardziej zaawansowane tematy dotyczące rozszerzania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="dc2e9-197">W sposób tworzenia i stosowania dodatkowych migracji.</span><span class="sxs-lookup"><span data-stu-id="dc2e9-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dc2e9-198">Tworzenie i stosowanie dodatkowych migracji</span><span class="sxs-lookup"><span data-stu-id="dc2e9-198">Create and apply additional migrations</span></span>](complex-data-model.md)
