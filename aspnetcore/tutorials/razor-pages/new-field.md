---
title: Część 7, Dodawanie nowego pola
author: rick-anderson
description: Część 7 serii samouczków na Razor stronach.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486164"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="a7d59-103">Część 7 Dodaj nowe pole do Razor strony w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7d59-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="a7d59-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7d59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7d59-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a7d59-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a7d59-106">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="a7d59-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a7d59-107">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-107">Add a new field to the model.</span></span>
* <span data-ttu-id="a7d59-108">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a7d59-109">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="a7d59-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a7d59-110">Dodaje [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="a7d59-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a7d59-111">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a7d59-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="a7d59-112">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a7d59-113">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="a7d59-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="a7d59-114">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="a7d59-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="a7d59-115">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-115">Build the app.</span></span>

1. <span data-ttu-id="a7d59-116">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="a7d59-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="a7d59-117">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="a7d59-117">Update the following pages:</span></span>
   1. <span data-ttu-id="a7d59-118">Dodaj `Rating` pole do stron usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="a7d59-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="a7d59-119">Zaktualizuj element [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) przy użyciu `Rating` pola.</span><span class="sxs-lookup"><span data-stu-id="a7d59-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="a7d59-120">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="a7d59-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a7d59-121">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="a7d59-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="a7d59-122">Uruchomienie aplikacji bez aktualizacji bazy danych zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="a7d59-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a7d59-123">`SqlException`Wyjątek jest spowodowany przez zaktualizowaną klasę filmów, która różni się od schematu tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a7d59-124">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="a7d59-125">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="a7d59-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a7d59-126">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a7d59-127">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a7d59-128">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a7d59-129">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="a7d59-130">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a7d59-131">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a7d59-132">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="a7d59-133">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a7d59-134">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a7d59-135">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="a7d59-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a7d59-136">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="a7d59-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a7d59-137">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="a7d59-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a7d59-138">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a7d59-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a7d59-139">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a7d59-140">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7d59-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a7d59-141">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="a7d59-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="a7d59-142">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="a7d59-143">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a7d59-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="a7d59-144">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="a7d59-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a7d59-145">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="a7d59-146">Utwórz kod, aby zmigrować schemat bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="a7d59-147">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a7d59-148">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a7d59-149">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="a7d59-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a7d59-150">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a7d59-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="a7d59-151">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="a7d59-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a7d59-152">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7d59-153">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="a7d59-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="a7d59-154">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="a7d59-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="a7d59-155">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="a7d59-156">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="a7d59-157">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-157">Select **OK**.</span></span>
1. <span data-ttu-id="a7d59-158">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a7d59-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a7d59-159">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a7d59-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a7d59-160">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="a7d59-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="a7d59-161">W tym samouczku użyjesz funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="a7d59-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a7d59-162">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a7d59-163">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="a7d59-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="a7d59-164">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a7d59-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="a7d59-165">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="a7d59-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a7d59-166">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="a7d59-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="a7d59-167">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="a7d59-168">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a7d59-169">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="a7d59-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="a7d59-170">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-170">Creating a new table.</span></span>
>* <span data-ttu-id="a7d59-171">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="a7d59-172">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-172">Dropping the old table.</span></span>
>* <span data-ttu-id="a7d59-173">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-173">Renaming the new table.</span></span>
>
><span data-ttu-id="a7d59-174">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="a7d59-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a7d59-175">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="a7d59-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a7d59-176">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="a7d59-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a7d59-177">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="a7d59-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a7d59-178">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="a7d59-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="a7d59-179">Usuń folder migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="a7d59-180">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="a7d59-181">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="a7d59-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a7d59-182">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7d59-183">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a7d59-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a7d59-184">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a7d59-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="a7d59-185">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a7d59-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a7d59-186">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="a7d59-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a7d59-187">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-187">Add a new field to the model.</span></span>
* <span data-ttu-id="a7d59-188">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a7d59-189">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="a7d59-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a7d59-190">Dodaje [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="a7d59-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a7d59-191">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a7d59-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="a7d59-192">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a7d59-193">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="a7d59-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="a7d59-194">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="a7d59-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="a7d59-195">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-195">Build the app.</span></span>

1. <span data-ttu-id="a7d59-196">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="a7d59-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="a7d59-197">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="a7d59-197">Update the following pages:</span></span>
   1. <span data-ttu-id="a7d59-198">Dodaj `Rating` pole do stron usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="a7d59-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="a7d59-199">Zaktualizuj element [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) przy użyciu `Rating` pola.</span><span class="sxs-lookup"><span data-stu-id="a7d59-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="a7d59-200">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="a7d59-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a7d59-201">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="a7d59-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="a7d59-202">Uruchomienie aplikacji bez aktualizacji bazy danych zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="a7d59-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a7d59-203">`SqlException`Wyjątek jest spowodowany przez zaktualizowaną klasę filmów, która różni się od schematu tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a7d59-204">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="a7d59-205">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="a7d59-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a7d59-206">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a7d59-207">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a7d59-208">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a7d59-209">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="a7d59-210">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a7d59-211">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a7d59-212">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="a7d59-213">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a7d59-214">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a7d59-215">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="a7d59-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a7d59-216">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="a7d59-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a7d59-217">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="a7d59-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a7d59-218">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a7d59-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a7d59-219">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a7d59-220">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7d59-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a7d59-221">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="a7d59-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="a7d59-222">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="a7d59-223">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a7d59-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="a7d59-224">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="a7d59-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a7d59-225">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="a7d59-226">Utwórz kod, aby zmigrować schemat bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="a7d59-227">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a7d59-228">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a7d59-229">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="a7d59-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a7d59-230">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a7d59-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="a7d59-231">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="a7d59-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a7d59-232">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7d59-233">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="a7d59-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="a7d59-234">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="a7d59-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="a7d59-235">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="a7d59-236">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="a7d59-237">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-237">Select **OK**.</span></span>
* <span data-ttu-id="a7d59-238">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a7d59-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a7d59-239">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a7d59-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a7d59-240">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="a7d59-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="a7d59-241">W tym samouczku należy użyć funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="a7d59-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a7d59-242">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a7d59-243">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="a7d59-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="a7d59-244">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a7d59-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="a7d59-245">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="a7d59-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a7d59-246">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="a7d59-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="a7d59-247">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="a7d59-248">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a7d59-249">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="a7d59-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="a7d59-250">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-250">Creating a new table.</span></span>
>* <span data-ttu-id="a7d59-251">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="a7d59-252">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-252">Dropping the old table.</span></span>
>* <span data-ttu-id="a7d59-253">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-253">Renaming the new table.</span></span>
>
><span data-ttu-id="a7d59-254">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="a7d59-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a7d59-255">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="a7d59-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a7d59-256">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="a7d59-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a7d59-257">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="a7d59-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a7d59-258">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="a7d59-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="a7d59-259">Usuń folder migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="a7d59-260">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="a7d59-261">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="a7d59-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a7d59-262">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7d59-263">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a7d59-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a7d59-264">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a7d59-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a7d59-265">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a7d59-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a7d59-266">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="a7d59-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a7d59-267">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-267">Add a new field to the model.</span></span>
* <span data-ttu-id="a7d59-268">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a7d59-269">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="a7d59-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a7d59-270">Dodaje [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="a7d59-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a7d59-271">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a7d59-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="a7d59-272">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a7d59-273">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="a7d59-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="a7d59-274">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="a7d59-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="a7d59-275">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-275">Build the app.</span></span>

<span data-ttu-id="a7d59-276">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="a7d59-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="a7d59-277">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="a7d59-277">Update the following pages:</span></span>

* <span data-ttu-id="a7d59-278">Dodaj `Rating` pole do stron usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="a7d59-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="a7d59-279">Zaktualizuj element [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) przy użyciu `Rating` pola.</span><span class="sxs-lookup"><span data-stu-id="a7d59-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="a7d59-280">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="a7d59-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a7d59-281">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="a7d59-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="a7d59-282">Jeśli aplikacja zostanie uruchomiona teraz, aplikacja zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="a7d59-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a7d59-283">Ten błąd jest spowodowany przez zaktualizowaną klasę filmu inną niż schemat tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a7d59-284">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="a7d59-285">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="a7d59-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a7d59-286">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a7d59-287">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a7d59-288">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a7d59-289">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="a7d59-290">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a7d59-291">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a7d59-292">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="a7d59-293">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a7d59-294">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a7d59-295">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="a7d59-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a7d59-296">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="a7d59-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a7d59-297">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="a7d59-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a7d59-298">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a7d59-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a7d59-299">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a7d59-300">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7d59-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a7d59-301">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="a7d59-301">Add a migration for the rating field</span></span>

<span data-ttu-id="a7d59-302">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="a7d59-303">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a7d59-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="a7d59-304">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="a7d59-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a7d59-305">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="a7d59-306">Utwórz kod, aby zmigrować schemat bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="a7d59-307">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a7d59-308">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="a7d59-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a7d59-309">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="a7d59-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a7d59-310">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a7d59-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="a7d59-311">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="a7d59-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a7d59-312">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7d59-313">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="a7d59-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="a7d59-314">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="a7d59-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="a7d59-315">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="a7d59-316">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="a7d59-317">Wybierz pozycję **OK**.</span><span class="sxs-lookup"><span data-stu-id="a7d59-317">Select **OK**.</span></span>
* <span data-ttu-id="a7d59-318">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a7d59-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a7d59-319">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a7d59-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a7d59-320">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="a7d59-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="a7d59-321">W tym samouczku należy użyć funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="a7d59-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="a7d59-322">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="a7d59-323">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="a7d59-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="a7d59-324">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a7d59-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="a7d59-325">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="a7d59-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a7d59-326">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="a7d59-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="a7d59-327">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="a7d59-328">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="a7d59-329">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="a7d59-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="a7d59-330">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-330">Creating a new table.</span></span>
>* <span data-ttu-id="a7d59-331">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="a7d59-332">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-332">Dropping the old table.</span></span>
>* <span data-ttu-id="a7d59-333">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a7d59-333">Renaming the new table.</span></span>
>
><span data-ttu-id="a7d59-334">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="a7d59-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="a7d59-335">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="a7d59-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a7d59-336">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="a7d59-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a7d59-337">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="a7d59-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="a7d59-338">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="a7d59-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="a7d59-339">Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a7d59-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a7d59-340">Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie. DB*).</span><span class="sxs-lookup"><span data-stu-id="a7d59-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="a7d59-341">Następnie uruchom `ef database update` polecenie:</span><span class="sxs-lookup"><span data-stu-id="a7d59-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="a7d59-342">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="a7d59-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a7d59-343">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="a7d59-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7d59-344">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a7d59-344">Additional resources</span></span>

* [<span data-ttu-id="a7d59-345">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="a7d59-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="a7d59-346">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a7d59-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
