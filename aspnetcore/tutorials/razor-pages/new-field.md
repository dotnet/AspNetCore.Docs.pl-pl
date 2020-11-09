---
title: Część 7, Dodawanie nowego pola
author: rick-anderson
description: 'Część 7 serii samouczków na Razor stronach.'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2dca5a9552dd2800212f8cd78ace0578b3d38cdb
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360882"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="96f1c-103">Część 7 Dodaj nowe pole do Razor strony w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96f1c-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="96f1c-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="96f1c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="96f1c-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="96f1c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="96f1c-106">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="96f1c-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="96f1c-107">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-107">Add a new field to the model.</span></span>
* <span data-ttu-id="96f1c-108">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="96f1c-109">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="96f1c-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="96f1c-110">Dodaje [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="96f1c-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="96f1c-111">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="96f1c-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="96f1c-112">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="96f1c-113">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="96f1c-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="96f1c-114">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="96f1c-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="96f1c-115">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-115">Build the app.</span></span>

1. <span data-ttu-id="96f1c-116">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="96f1c-116">Edit *Pages/Movies/Index.cshtml* , and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="96f1c-117">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="96f1c-117">Update the following pages:</span></span>
   1. <span data-ttu-id="96f1c-118">Dodaj `Rating` pole do Delete stron i szczegółów.</span><span class="sxs-lookup"><span data-stu-id="96f1c-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="96f1c-119">Update [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="96f1c-120">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="96f1c-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="96f1c-121">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="96f1c-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="96f1c-122">Uruchomienie aplikacji bez aktualizacji bazy danych zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="96f1c-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="96f1c-123">`SqlException`Wyjątek jest spowodowany przez zaktualizowaną klasę filmów, która różni się od schematu tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="96f1c-124">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="96f1c-125">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="96f1c-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="96f1c-126">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="96f1c-127">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="96f1c-128">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="96f1c-129">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="96f1c-130">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="96f1c-131">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="96f1c-132">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="96f1c-133">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="96f1c-134">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="96f1c-135">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="96f1c-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="96f1c-136">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="96f1c-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="96f1c-137">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="96f1c-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="96f1c-138">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="96f1c-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="96f1c-139">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96f1c-140">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96f1c-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="96f1c-141">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="96f1c-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="96f1c-142">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="96f1c-143">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96f1c-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="96f1c-144">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="96f1c-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="96f1c-145">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="96f1c-146">Create kod do migracji schematu bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="96f1c-147">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="96f1c-148">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="96f1c-149">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="96f1c-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="96f1c-150">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="96f1c-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="96f1c-151">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="96f1c-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="96f1c-152">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="96f1c-153">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="96f1c-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="96f1c-154">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="96f1c-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="96f1c-155">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Delete** .</span><span class="sxs-lookup"><span data-stu-id="96f1c-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="96f1c-156">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="96f1c-157">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-157">Select **OK**.</span></span>
1. <span data-ttu-id="96f1c-158">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="96f1c-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96f1c-159">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96f1c-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="96f1c-160">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="96f1c-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="96f1c-161">W tym samouczku użyjesz funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="96f1c-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="96f1c-162">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="96f1c-163">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="96f1c-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="96f1c-164">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="96f1c-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="96f1c-165">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="96f1c-166">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="96f1c-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="96f1c-167">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="96f1c-168">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="96f1c-169">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="96f1c-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="96f1c-170">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-170">Creating a new table.</span></span>
>* <span data-ttu-id="96f1c-171">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="96f1c-172">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-172">Dropping the old table.</span></span>
>* <span data-ttu-id="96f1c-173">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-173">Renaming the new table.</span></span>
>
><span data-ttu-id="96f1c-174">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="96f1c-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="96f1c-175">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="96f1c-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="96f1c-176">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="96f1c-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="96f1c-177">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="96f1c-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="96f1c-178">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="96f1c-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="96f1c-179">Delete folder migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="96f1c-180">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="96f1c-181">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="96f1c-182">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96f1c-183">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96f1c-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="96f1c-184">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="96f1c-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="96f1c-185">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="96f1c-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="96f1c-186">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="96f1c-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="96f1c-187">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-187">Add a new field to the model.</span></span>
* <span data-ttu-id="96f1c-188">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="96f1c-189">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="96f1c-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="96f1c-190">Dodaje [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="96f1c-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="96f1c-191">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="96f1c-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="96f1c-192">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="96f1c-193">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="96f1c-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="96f1c-194">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="96f1c-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="96f1c-195">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-195">Build the app.</span></span>

1. <span data-ttu-id="96f1c-196">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="96f1c-196">Edit *Pages/Movies/Index.cshtml* , and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="96f1c-197">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="96f1c-197">Update the following pages:</span></span>
   1. <span data-ttu-id="96f1c-198">Dodaj `Rating` pole do Delete stron i szczegółów.</span><span class="sxs-lookup"><span data-stu-id="96f1c-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="96f1c-199">Update [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="96f1c-200">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="96f1c-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="96f1c-201">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="96f1c-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="96f1c-202">Uruchomienie aplikacji bez aktualizacji bazy danych zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="96f1c-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="96f1c-203">`SqlException`Wyjątek jest spowodowany przez zaktualizowaną klasę filmów, która różni się od schematu tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="96f1c-204">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="96f1c-205">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="96f1c-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="96f1c-206">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="96f1c-207">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="96f1c-208">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="96f1c-209">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="96f1c-210">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="96f1c-211">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="96f1c-212">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="96f1c-213">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="96f1c-214">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="96f1c-215">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="96f1c-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="96f1c-216">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="96f1c-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="96f1c-217">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="96f1c-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="96f1c-218">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="96f1c-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="96f1c-219">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96f1c-220">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96f1c-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="96f1c-221">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="96f1c-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="96f1c-222">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="96f1c-223">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96f1c-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="96f1c-224">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="96f1c-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="96f1c-225">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="96f1c-226">Create kod do migracji schematu bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="96f1c-227">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="96f1c-228">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="96f1c-229">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="96f1c-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="96f1c-230">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="96f1c-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="96f1c-231">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="96f1c-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="96f1c-232">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="96f1c-233">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="96f1c-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="96f1c-234">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="96f1c-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="96f1c-235">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Delete** .</span><span class="sxs-lookup"><span data-stu-id="96f1c-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="96f1c-236">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="96f1c-237">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-237">Select **OK**.</span></span>
* <span data-ttu-id="96f1c-238">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="96f1c-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96f1c-239">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96f1c-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="96f1c-240">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="96f1c-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="96f1c-241">W tym samouczku należy użyć funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="96f1c-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="96f1c-242">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="96f1c-243">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="96f1c-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="96f1c-244">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="96f1c-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="96f1c-245">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="96f1c-246">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="96f1c-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="96f1c-247">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="96f1c-248">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="96f1c-249">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="96f1c-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="96f1c-250">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-250">Creating a new table.</span></span>
>* <span data-ttu-id="96f1c-251">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="96f1c-252">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-252">Dropping the old table.</span></span>
>* <span data-ttu-id="96f1c-253">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-253">Renaming the new table.</span></span>
>
><span data-ttu-id="96f1c-254">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="96f1c-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="96f1c-255">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="96f1c-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="96f1c-256">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="96f1c-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="96f1c-257">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="96f1c-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="96f1c-258">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="96f1c-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="96f1c-259">Delete folder migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="96f1c-260">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="96f1c-261">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="96f1c-262">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96f1c-263">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96f1c-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="96f1c-264">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="96f1c-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="96f1c-265">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="96f1c-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="96f1c-266">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="96f1c-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="96f1c-267">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-267">Add a new field to the model.</span></span>
* <span data-ttu-id="96f1c-268">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="96f1c-269">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="96f1c-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="96f1c-270">Dodaje [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="96f1c-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="96f1c-271">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="96f1c-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="96f1c-272">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="96f1c-273">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="96f1c-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="96f1c-274">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="96f1c-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="96f1c-275">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-275">Build the app.</span></span>

<span data-ttu-id="96f1c-276">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="96f1c-276">Edit *Pages/Movies/Index.cshtml* , and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="96f1c-277">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="96f1c-277">Update the following pages:</span></span>

* <span data-ttu-id="96f1c-278">Dodaj `Rating` pole do Delete stron i szczegółów.</span><span class="sxs-lookup"><span data-stu-id="96f1c-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="96f1c-279">Update [ Create . cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="96f1c-280">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="96f1c-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="96f1c-281">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="96f1c-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="96f1c-282">Jeśli aplikacja zostanie uruchomiona teraz, aplikacja zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="96f1c-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="96f1c-283">Ten błąd jest spowodowany przez zaktualizowaną klasę filmu inną niż schemat tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="96f1c-284">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="96f1c-285">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="96f1c-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="96f1c-286">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="96f1c-287">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="96f1c-288">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="96f1c-289">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="96f1c-290">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="96f1c-291">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="96f1c-292">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="96f1c-293">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="96f1c-294">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="96f1c-295">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="96f1c-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="96f1c-296">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="96f1c-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="96f1c-297">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="96f1c-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="96f1c-298">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="96f1c-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="96f1c-299">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96f1c-300">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96f1c-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="96f1c-301">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="96f1c-301">Add a migration for the rating field</span></span>

<span data-ttu-id="96f1c-302">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="96f1c-303">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96f1c-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="96f1c-304">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="96f1c-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="96f1c-305">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="96f1c-306">Create kod do migracji schematu bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="96f1c-307">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="96f1c-308">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="96f1c-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="96f1c-309">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="96f1c-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="96f1c-310">W przypadku usunięcia wszystkich rekordów w DdatabaseB inicjator będzie wypełniać DdatabaseB i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="96f1c-310">If you delete all the records in the DdatabaseB, the initializer will seed the DdatabaseB and include the `Rating` field.</span></span> <span data-ttu-id="96f1c-311">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="96f1c-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="96f1c-312">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="96f1c-313">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="96f1c-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="96f1c-314">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="96f1c-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="96f1c-315">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Delete** .</span><span class="sxs-lookup"><span data-stu-id="96f1c-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="96f1c-316">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="96f1c-317">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="96f1c-317">Select **OK**.</span></span>
* <span data-ttu-id="96f1c-318">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="96f1c-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96f1c-319">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="96f1c-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="96f1c-320">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="96f1c-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="96f1c-321">W tym samouczku należy użyć funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="96f1c-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="96f1c-322">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="96f1c-323">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="96f1c-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="96f1c-324">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="96f1c-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="96f1c-325">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="96f1c-326">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="96f1c-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="96f1c-327">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="96f1c-328">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="96f1c-329">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="96f1c-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="96f1c-330">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-330">Creating a new table.</span></span>
>* <span data-ttu-id="96f1c-331">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="96f1c-332">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-332">Dropping the old table.</span></span>
>* <span data-ttu-id="96f1c-333">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="96f1c-333">Renaming the new table.</span></span>
>
><span data-ttu-id="96f1c-334">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="96f1c-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="96f1c-335">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="96f1c-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="96f1c-336">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="96f1c-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="96f1c-337">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="96f1c-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="96f1c-338">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="96f1c-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="96f1c-339">Delete Baza danych programu i użyj migracji, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96f1c-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="96f1c-340">Aby usunąć bazę danych, usuń plik bazy danych ( *MvcMovie. DB* ).</span><span class="sxs-lookup"><span data-stu-id="96f1c-340">To delete the database, delete the database file ( *MvcMovie.db* ).</span></span> <span data-ttu-id="96f1c-341">Następnie uruchom `ef database update` polecenie:</span><span class="sxs-lookup"><span data-stu-id="96f1c-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="96f1c-342">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="96f1c-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="96f1c-343">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="96f1c-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96f1c-344">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96f1c-344">Additional resources</span></span>

* [<span data-ttu-id="96f1c-345">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="96f1c-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="96f1c-346">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="96f1c-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
