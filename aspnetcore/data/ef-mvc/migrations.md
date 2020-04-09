---
title: 'Samouczek: Korzystanie z funkcji migracji - ASP.NET MVC z EF Core'
description: W tym samouczku można rozpocząć korzystanie z funkcji migracji EF Core do zarządzania zmianami modelu danych w aplikacji ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 8b3417205457a5ce5fa16994701a06e2a4d7d350
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665726"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f0b6f-103">Samouczek: Korzystanie z funkcji migracji - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="f0b6f-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f0b6f-104">W tym samouczku można rozpocząć korzystanie z funkcji migracji ef core do zarządzania zmianami modelu danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="f0b6f-105">W późniejszych samouczkach dodasz więcej migracji podczas zmiany modelu danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="f0b6f-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f0b6f-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0b6f-107">Dowiedz się więcej o migracjach</span><span class="sxs-lookup"><span data-stu-id="f0b6f-107">Learn about migrations</span></span>
> * <span data-ttu-id="f0b6f-108">Zmienianie ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="f0b6f-108">Change the connection string</span></span>
> * <span data-ttu-id="f0b6f-109">Tworzenie migracji początkowej</span><span class="sxs-lookup"><span data-stu-id="f0b6f-109">Create an initial migration</span></span>
> * <span data-ttu-id="f0b6f-110">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="f0b6f-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="f0b6f-111">Dowiedz się więcej o migawce modelu danych</span><span class="sxs-lookup"><span data-stu-id="f0b6f-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="f0b6f-112">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="f0b6f-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f0b6f-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f0b6f-113">Prerequisites</span></span>

* [<span data-ttu-id="f0b6f-114">Sortowanie, filtrowanie i stronicowanie</span><span class="sxs-lookup"><span data-stu-id="f0b6f-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="f0b6f-115">Informacje o migracjach</span><span class="sxs-lookup"><span data-stu-id="f0b6f-115">About migrations</span></span>

<span data-ttu-id="f0b6f-116">Podczas tworzenia nowej aplikacji, model danych zmienia się często i za każdym razem, gdy zmienia się model, pobiera zsynchronizowane z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="f0b6f-117">Rozpoczęto te samouczki, konfigurując entity framework do tworzenia bazy danych, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="f0b6f-118">Następnie za każdym razem, gdy zmienisz model danych - dodaj, usuń lub zmień klasy jednostek lub zmień klasę DbContext - możesz usunąć bazę danych, a EF utworzy nową, która pasuje do modelu, i zasiewa ją danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="f0b6f-119">Ta metoda utrzymywania bazy danych w synchronizacji z modelem danych działa dobrze, dopóki nie wdrożysz aplikacji w procesach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="f0b6f-120">Gdy aplikacja jest uruchomiona w produkcji jest zwykle przechowywanie danych, które chcesz zachować i nie chcesz stracić wszystko za każdym razem, gdy wprowadzasz zmiany, takie jak dodawanie nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="f0b6f-121">Funkcja Migracji rdzenia EF rozwiązuje ten problem, umożliwiając EF zaktualizować schemat bazy danych zamiast tworzenia nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="f0b6f-122">Aby pracować z migracjami, można użyć **konsoli Menedżera pakietów** (PMC) lub interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="f0b6f-123">Te samouczki pokazują, jak używać poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="f0b6f-124">Informacje o PMC znajduje [się na końcu tego samouczka](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f0b6f-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="f0b6f-125">Zmienianie ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="f0b6f-125">Change the connection string</span></span>

<span data-ttu-id="f0b6f-126">W pliku *appsettings.json* zmień nazwę bazy danych w ciągu połączenia na ContosoUniversity2 lub inną nazwę, która nie została użyta na komputerze, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="f0b6f-127">Ta zmiana konfiguruje projekt, tak aby pierwsza migracja utworzyła nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="f0b6f-128">Nie jest to wymagane, aby rozpocząć migracje, ale zobaczysz później, dlaczego jest to dobry pomysł.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="f0b6f-129">Jako alternatywę dla zmiany nazwy bazy danych można usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="f0b6f-130">Użyj **programu SQL Server Object Explorer** `database drop` (SSOX) lub polecenia CLI:</span><span class="sxs-lookup"><span data-stu-id="f0b6f-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="f0b6f-131">W poniższej sekcji wyjaśniono, jak uruchomić polecenia interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="f0b6f-132">Tworzenie migracji początkowej</span><span class="sxs-lookup"><span data-stu-id="f0b6f-132">Create an initial migration</span></span>

<span data-ttu-id="f0b6f-133">Zapisz swoje zmiany i zbuduj projekt.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-133">Save your changes and build the project.</span></span> <span data-ttu-id="f0b6f-134">Następnie otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="f0b6f-135">Oto szybki sposób, aby to zrobić:</span><span class="sxs-lookup"><span data-stu-id="f0b6f-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="f0b6f-136">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Otwórz folder w Eksploratorze plików** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Otwórz w eksploratorze plików element menu](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="f0b6f-138">Wprowadź "cmd" na pasku adresu i naciśnij klawisz Enter.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Otwórz okno polecenia](migrations/_static/open-command-window.png)

<span data-ttu-id="f0b6f-140">W oknie polecenia wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f0b6f-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="f0b6f-141">W oknie polecenia widoczne są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="f0b6f-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="f0b6f-142">Jeśli zostanie wyświetlony komunikat o błędzie *Brak pliku wykonywalnego znaleziono pasujące polecenie "dotnet-ef",* zobacz ten wpis w [blogu,](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) aby uzyskać pomoc w rozwiązywaniu problemów.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="f0b6f-143">Jeśli zostanie wyświetlony komunikat o błędzie "*nie można uzyskać dostępu do pliku ... Plik ContosoUniversity.dll, ponieważ jest używany przez inny proces.*" znajdź ikonę IIS Express w zasobniku systemowym systemu Windows, a następnie kliknij ją prawym przyciskiem myszy, a następnie kliknij polecenie **ContosoUniversity > Zatrzymaj witrynę**.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="f0b6f-144">Sprawdzanie metod w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="f0b6f-144">Examine Up and Down methods</span></span>

<span data-ttu-id="f0b6f-145">Podczas wykonywania `migrations add` polecenia EF wygenerował kod, który utworzy bazę danych od podstaw.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="f0b6f-146">Ten kod znajduje się w folderze *Migracje,* w pliku o nazwie \* \<sygnatura czasowa>_InitialCreate.cs\*.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="f0b6f-147">Metoda `Up` `InitialCreate` klasy tworzy tabele bazy danych, które odpowiadają zestawom `Down` jednostek modelu danych, a metoda usuwa je, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="f0b6f-148">Migracje wywołuje `Up` metodę zaimplementowania zmian modelu danych dla migracji.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="f0b6f-149">Po wprowadzeniu polecenia, aby wycofać aktualizację, `Down` Migracje wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="f0b6f-150">Ten kod jest przeznaczony dla migracji początkowej, `migrations add InitialCreate` która została utworzona po wprowadzeniu polecenia.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="f0b6f-151">Parametr nazwa migracji ("InitialCreate" w przykładzie) jest używany dla nazwy pliku i może być dowolny.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="f0b6f-152">Najlepiej wybrać słowo lub frazę, która podsumowuje to, co jest wykonywane podczas migracji.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="f0b6f-153">Na przykład można nazwać późniejszą migrację "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="f0b6f-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="f0b6f-154">Jeśli migracja początkowa została utworzona, gdy baza danych już istnieje, kod tworzenia bazy danych jest generowany, ale nie musi być uruchamiany, ponieważ baza danych jest już zgodna z modelem danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="f0b6f-155">Po wdrożeniu aplikacji do innego środowiska, w którym baza danych jeszcze nie istnieje, ten kod zostanie uruchomiony w celu utworzenia bazy danych, więc warto najpierw ją przetestować.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="f0b6f-156">Dlatego wcześniej zmieniono nazwę bazy danych w ciągu połączenia , aby migracje mogły utworzyć nową od podstaw.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="f0b6f-157">Migawka modelu danych</span><span class="sxs-lookup"><span data-stu-id="f0b6f-157">The data model snapshot</span></span>

<span data-ttu-id="f0b6f-158">Migracje tworzy *migawkę* bieżącego schematu bazy danych w *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="f0b6f-159">Po dodaniu migracji EF określa, co się zmieniło, porównując model danych z plikiem migawki.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="f0b6f-160">Użyj polecenia [usuwania migracji dotnet ef,](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) aby usunąć migrację.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="f0b6f-161">`dotnet ef migrations remove`usuwa migrację i zapewnia prawidłowe zresetowanie migawki.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="f0b6f-162">Jeśli `dotnet ef migrations remove` nie `dotnet ef migrations remove -v` powiedzie się, użyj, aby uzyskać więcej informacji na temat błędu.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="f0b6f-163">Zobacz [EF Core migracji w środowiskach zespołu,](/ef/core/managing-schemas/migrations/teams) aby uzyskać więcej informacji na temat sposobu użycia pliku migawki.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="f0b6f-164">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="f0b6f-164">Apply the migration</span></span>

<span data-ttu-id="f0b6f-165">W oknie polecenia wprowadź następujące polecenie, aby utworzyć w niej bazę danych i tabele.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="f0b6f-166">Dane wyjściowe z polecenia `migrations add` jest podobny do polecenia, z tą różnicą, że są widoczne dzienniki dla poleceń SQL, które skonfigurować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="f0b6f-167">Większość dzienników są pomijane w następujących danych wyjściowych przykładu.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="f0b6f-168">Jeśli nie chcesz, aby ten poziom szczegółowości w komunikatach dziennika, można zmienić poziom dziennika w *appsettings. Development.json.*</span><span class="sxs-lookup"><span data-stu-id="f0b6f-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="f0b6f-169">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

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

<span data-ttu-id="f0b6f-170">Użyj **Eksploratora obiektów programu SQL Server,** aby sprawdzić bazę danych, tak jak w pierwszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="f0b6f-171">Można zauważyć dodanie \_ \_TABELI EFMigrationsHistory, która przechowuje śledzenie, które migracje zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="f0b6f-172">Wyświetl dane w tej tabeli, a zobaczysz jeden wiersz dla pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="f0b6f-173">(Ostatni dziennik w poprzednim przykładzie wyjściowym interfejsu wiersza polecenia pokazuje insert instrukcji, która tworzy ten wiersz.)</span><span class="sxs-lookup"><span data-stu-id="f0b6f-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="f0b6f-174">Uruchom aplikację, aby sprawdzić, czy wszystko nadal działa tak samo jak poprzednio.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-174">Run the application to verify that everything still works the same as before.</span></span>

![Strona Indeks uczniów](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="f0b6f-176">Porównaj CLI i PMC</span><span class="sxs-lookup"><span data-stu-id="f0b6f-176">Compare CLI and PMC</span></span>

<span data-ttu-id="f0b6f-177">Narzędzia EF do zarządzania migracjami są dostępne w poleceniach interfejsu wiersza polecenia .NET Core lub z poleceń cmdlet programu PowerShell w oknie konsoli programu Visual Studio **Package Manager** (PMC).</span><span class="sxs-lookup"><span data-stu-id="f0b6f-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="f0b6f-178">Ten samouczek pokazuje, jak korzystać z interfejsu wiersza polecenia, ale można użyć PMC, jeśli wolisz.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="f0b6f-179">Polecenia EF dla poleceń PMC znajdują się w pakiecie [Microsoft.EntityFrameworkCore.Tools.](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)</span><span class="sxs-lookup"><span data-stu-id="f0b6f-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="f0b6f-180">Ten pakiet znajduje się w [metapakietie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)więc nie trzeba dodawać odwołania do `Microsoft.AspNetCore.App`pakietu, jeśli aplikacja ma odwołanie do pakietu .</span><span class="sxs-lookup"><span data-stu-id="f0b6f-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="f0b6f-181">**Ważne:** Nie jest to ten sam pakiet, który instalujesz dla interfejsu wiersza polecenia, edytując plik *csproj.*</span><span class="sxs-lookup"><span data-stu-id="f0b6f-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="f0b6f-182">Nazwa tego kończy się `Tools`na , w przeciwieństwie `Tools.DotNet`do nazwy pakietu CLI, która kończy się na .</span><span class="sxs-lookup"><span data-stu-id="f0b6f-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="f0b6f-183">Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="f0b6f-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="f0b6f-184">Aby uzyskać więcej informacji na temat poleceń PMC, zobacz [Konsola Menedżera pakietów (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="f0b6f-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f0b6f-185">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="f0b6f-185">Get the code</span></span>

[<span data-ttu-id="f0b6f-186">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="f0b6f-187">Następny krok</span><span class="sxs-lookup"><span data-stu-id="f0b6f-187">Next step</span></span>

<span data-ttu-id="f0b6f-188">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f0b6f-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0b6f-189">Dowiedz się więcej o migracjach</span><span class="sxs-lookup"><span data-stu-id="f0b6f-189">Learned about migrations</span></span>
> * <span data-ttu-id="f0b6f-190">Dowiedz się więcej o pakietach migracji NuGet</span><span class="sxs-lookup"><span data-stu-id="f0b6f-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="f0b6f-191">Zmieniono parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="f0b6f-191">Changed the connection string</span></span>
> * <span data-ttu-id="f0b6f-192">Utworzono migrację początkową</span><span class="sxs-lookup"><span data-stu-id="f0b6f-192">Created an initial migration</span></span>
> * <span data-ttu-id="f0b6f-193">Zbadane metody w górę i w dół</span><span class="sxs-lookup"><span data-stu-id="f0b6f-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="f0b6f-194">Dowiedz się więcej o migawce modelu danych</span><span class="sxs-lookup"><span data-stu-id="f0b6f-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="f0b6f-195">Zastosowano migrację</span><span class="sxs-lookup"><span data-stu-id="f0b6f-195">Applied the migration</span></span>

<span data-ttu-id="f0b6f-196">Przejdź do następnego samouczka, aby rozpocząć przeglądanie bardziej zaawansowanych tematów dotyczących rozszerzania modelu danych.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="f0b6f-197">Po drodze utworzysz i zastosujesz dodatkowe migracje.</span><span class="sxs-lookup"><span data-stu-id="f0b6f-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f0b6f-198">Tworzenie i stosowanie dodatkowych migracji</span><span class="sxs-lookup"><span data-stu-id="f0b6f-198">Create and apply additional migrations</span></span>](complex-data-model.md)
