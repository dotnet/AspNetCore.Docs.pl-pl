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
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101149"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="95f3d-103">Część 7 Dodaj nowe pole do Razor strony w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95f3d-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="95f3d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="95f3d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="95f3d-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95f3d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="95f3d-106">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="95f3d-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="95f3d-107">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-107">Add a new field to the model.</span></span>
* <span data-ttu-id="95f3d-108">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="95f3d-109">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="95f3d-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="95f3d-110">Dodaje [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="95f3d-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="95f3d-111">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="95f3d-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="95f3d-112">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="95f3d-113">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="95f3d-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="95f3d-114">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="95f3d-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="95f3d-115">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-115">Build the app.</span></span>

1. <span data-ttu-id="95f3d-116">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="95f3d-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="95f3d-117">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="95f3d-117">Update the following pages:</span></span>
   1. <span data-ttu-id="95f3d-118">Dodaj `Rating` pole do stron usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="95f3d-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="95f3d-119">Zaktualizuj element [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) przy użyciu `Rating` pola.</span><span class="sxs-lookup"><span data-stu-id="95f3d-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="95f3d-120">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="95f3d-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="95f3d-121">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="95f3d-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="95f3d-122">Uruchomienie aplikacji bez aktualizacji bazy danych zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="95f3d-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="95f3d-123">`SqlException`Wyjątek jest spowodowany przez zaktualizowaną klasę filmów, która różni się od schematu tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="95f3d-124">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="95f3d-125">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="95f3d-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="95f3d-126">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="95f3d-127">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="95f3d-128">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="95f3d-129">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="95f3d-130">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="95f3d-131">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="95f3d-132">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="95f3d-133">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="95f3d-134">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="95f3d-135">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="95f3d-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="95f3d-136">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="95f3d-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="95f3d-137">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="95f3d-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="95f3d-138">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="95f3d-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="95f3d-139">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95f3d-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95f3d-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="95f3d-141">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="95f3d-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="95f3d-142">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="95f3d-143">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="95f3d-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="95f3d-144">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="95f3d-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="95f3d-145">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="95f3d-146">Utwórz kod, aby zmigrować schemat bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="95f3d-147">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="95f3d-148">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="95f3d-149">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="95f3d-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="95f3d-150">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="95f3d-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="95f3d-151">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="95f3d-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="95f3d-152">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="95f3d-153">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="95f3d-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="95f3d-154">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="95f3d-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="95f3d-155">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="95f3d-156">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="95f3d-157">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-157">Select **OK**.</span></span>
1. <span data-ttu-id="95f3d-158">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="95f3d-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="95f3d-159">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="95f3d-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="95f3d-160">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="95f3d-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="95f3d-161">W tym samouczku użyjesz funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="95f3d-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="95f3d-162">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="95f3d-163">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="95f3d-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="95f3d-164">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="95f3d-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="95f3d-165">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="95f3d-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="95f3d-166">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="95f3d-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="95f3d-167">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="95f3d-168">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="95f3d-169">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="95f3d-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="95f3d-170">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-170">Creating a new table.</span></span>
>* <span data-ttu-id="95f3d-171">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="95f3d-172">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-172">Dropping the old table.</span></span>
>* <span data-ttu-id="95f3d-173">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-173">Renaming the new table.</span></span>
>
><span data-ttu-id="95f3d-174">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="95f3d-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="95f3d-175">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="95f3d-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="95f3d-176">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="95f3d-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="95f3d-177">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="95f3d-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="95f3d-178">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="95f3d-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="95f3d-179">Usuń folder migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="95f3d-180">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="95f3d-181">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="95f3d-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="95f3d-182">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95f3d-183">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="95f3d-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="95f3d-184">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="95f3d-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="95f3d-185">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95f3d-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="95f3d-186">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="95f3d-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="95f3d-187">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-187">Add a new field to the model.</span></span>
* <span data-ttu-id="95f3d-188">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="95f3d-189">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="95f3d-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="95f3d-190">Dodaje [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="95f3d-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="95f3d-191">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="95f3d-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="95f3d-192">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="95f3d-193">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="95f3d-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="95f3d-194">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="95f3d-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="95f3d-195">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-195">Build the app.</span></span>

1. <span data-ttu-id="95f3d-196">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="95f3d-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="95f3d-197">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="95f3d-197">Update the following pages:</span></span>
   1. <span data-ttu-id="95f3d-198">Dodaj `Rating` pole do stron usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="95f3d-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="95f3d-199">Zaktualizuj element [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) przy użyciu `Rating` pola.</span><span class="sxs-lookup"><span data-stu-id="95f3d-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="95f3d-200">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="95f3d-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="95f3d-201">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="95f3d-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="95f3d-202">Uruchomienie aplikacji bez aktualizacji bazy danych zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="95f3d-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="95f3d-203">`SqlException`Wyjątek jest spowodowany przez zaktualizowaną klasę filmów, która różni się od schematu tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="95f3d-204">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="95f3d-205">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="95f3d-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="95f3d-206">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="95f3d-207">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="95f3d-208">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="95f3d-209">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="95f3d-210">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="95f3d-211">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="95f3d-212">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="95f3d-213">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="95f3d-214">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="95f3d-215">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="95f3d-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="95f3d-216">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="95f3d-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="95f3d-217">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="95f3d-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="95f3d-218">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="95f3d-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="95f3d-219">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95f3d-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95f3d-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="95f3d-221">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="95f3d-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="95f3d-222">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="95f3d-223">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="95f3d-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="95f3d-224">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="95f3d-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="95f3d-225">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="95f3d-226">Utwórz kod, aby zmigrować schemat bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="95f3d-227">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="95f3d-228">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="95f3d-229">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="95f3d-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="95f3d-230">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="95f3d-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="95f3d-231">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="95f3d-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="95f3d-232">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="95f3d-233">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="95f3d-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="95f3d-234">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="95f3d-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="95f3d-235">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="95f3d-236">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="95f3d-237">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-237">Select **OK**.</span></span>
* <span data-ttu-id="95f3d-238">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="95f3d-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="95f3d-239">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="95f3d-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="95f3d-240">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="95f3d-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="95f3d-241">W tym samouczku należy użyć funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="95f3d-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="95f3d-242">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="95f3d-243">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="95f3d-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="95f3d-244">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="95f3d-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="95f3d-245">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="95f3d-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="95f3d-246">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="95f3d-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="95f3d-247">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="95f3d-248">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="95f3d-249">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="95f3d-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="95f3d-250">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-250">Creating a new table.</span></span>
>* <span data-ttu-id="95f3d-251">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="95f3d-252">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-252">Dropping the old table.</span></span>
>* <span data-ttu-id="95f3d-253">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-253">Renaming the new table.</span></span>
>
><span data-ttu-id="95f3d-254">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="95f3d-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="95f3d-255">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="95f3d-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="95f3d-256">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="95f3d-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="95f3d-257">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="95f3d-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="95f3d-258">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="95f3d-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="95f3d-259">Usuń folder migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="95f3d-260">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="95f3d-261">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="95f3d-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="95f3d-262">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95f3d-263">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="95f3d-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="95f3d-264">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="95f3d-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95f3d-265">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95f3d-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="95f3d-266">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migracje Code First służy do:</span><span class="sxs-lookup"><span data-stu-id="95f3d-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="95f3d-267">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-267">Add a new field to the model.</span></span>
* <span data-ttu-id="95f3d-268">Migruj nową zmianę schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="95f3d-269">W przypadku automatycznego tworzenia bazy danych przy użyciu narzędzia EF Code First Code First:</span><span class="sxs-lookup"><span data-stu-id="95f3d-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="95f3d-270">Dodaje [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) tabelę do bazy danych, aby sprawdzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z których została wygenerowana.</span><span class="sxs-lookup"><span data-stu-id="95f3d-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="95f3d-271">Jeśli klasy modelu nie są zsynchronizowane z bazą danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="95f3d-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="95f3d-272">Automatyczne Weryfikowanie, czy schemat i model są zsynchronizowane ułatwiają znajdowanie niespójnych problemów z kodem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="95f3d-273">Dodawanie właściwości oceny do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="95f3d-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="95f3d-274">Otwórz plik *models/Movie. cs* i Dodaj `Rating` Właściwość:</span><span class="sxs-lookup"><span data-stu-id="95f3d-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="95f3d-275">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-275">Build the app.</span></span>

<span data-ttu-id="95f3d-276">Edytowanie *stron/filmów/ Index . cshtml* i Dodawanie `Rating` pola:</span><span class="sxs-lookup"><span data-stu-id="95f3d-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="95f3d-277">Aktualizowanie następujących stron:</span><span class="sxs-lookup"><span data-stu-id="95f3d-277">Update the following pages:</span></span>

* <span data-ttu-id="95f3d-278">Dodaj `Rating` pole do stron usuwanie i szczegóły.</span><span class="sxs-lookup"><span data-stu-id="95f3d-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="95f3d-279">Zaktualizuj element [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) przy użyciu `Rating` pola.</span><span class="sxs-lookup"><span data-stu-id="95f3d-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="95f3d-280">Dodaj `Rating` pole do strony Edycja.</span><span class="sxs-lookup"><span data-stu-id="95f3d-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="95f3d-281">Aplikacja nie będzie działała, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="95f3d-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="95f3d-282">Jeśli aplikacja zostanie uruchomiona teraz, aplikacja zgłasza `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="95f3d-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="95f3d-283">Ten błąd jest spowodowany przez zaktualizowaną klasę filmu inną niż schemat tabeli filmów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="95f3d-284">Brak `Rating` kolumn w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="95f3d-285">Istnieje kilka metod rozpoznawania błędu:</span><span class="sxs-lookup"><span data-stu-id="95f3d-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="95f3d-286">Entity Framework automatycznie porzucić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="95f3d-287">Takie podejście jest wygodnie wczesne w cyklu programowania, dzięki czemu można szybko rozwijać model i schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="95f3d-288">Minusem polega na utracie istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="95f3d-289">Nie używaj tego podejścia w produkcyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="95f3d-290">Usunięcie bazy danych ze zmian schematu i użycie inicjatora do automatycznego umieszczania bazy danych z danymi testowymi jest często wydajnym sposobem na tworzenie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="95f3d-291">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby pasował do klas modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="95f3d-292">Zaletą tego podejścia jest zachowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="95f3d-293">Wprowadź tę zmianę ręcznie lub przez utworzenie skryptu zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="95f3d-294">Użyj Migracje Code First, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="95f3d-295">Na potrzeby tego samouczka Użyj Migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="95f3d-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="95f3d-296">Zaktualizuj `SeedData` klasę, aby zapewnić wartość nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="95f3d-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="95f3d-297">Poniżej przedstawiono przykładową zmianę, ale wprowadź tę zmianę dla każdego `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="95f3d-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="95f3d-298">Zobacz [ukończony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="95f3d-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="95f3d-299">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95f3d-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95f3d-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="95f3d-301">Dodawanie migracji dla pola oceny</span><span class="sxs-lookup"><span data-stu-id="95f3d-301">Add a migration for the rating field</span></span>

<span data-ttu-id="95f3d-302">W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="95f3d-303">W obszarze PMC wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="95f3d-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="95f3d-304">`Add-Migration`Polecenie informuje platformę, aby:</span><span class="sxs-lookup"><span data-stu-id="95f3d-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="95f3d-305">Porównaj `Movie` model ze `Movie` schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="95f3d-306">Utwórz kod, aby zmigrować schemat bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="95f3d-307">Nazwa "Rating" jest arbitralna i jest używana do nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="95f3d-308">Warto użyć zrozumiałej nazwy dla pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="95f3d-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="95f3d-309">`Update-Database`Polecenie informuje platformę, aby zastosować zmiany schematu do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="95f3d-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="95f3d-310">W przypadku usunięcia wszystkich rekordów w bazie danych inicjator będzie wypełniać bazę danych i zawierać `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="95f3d-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="95f3d-311">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [programu SQL Server Eksplorator obiektów](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="95f3d-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="95f3d-312">Innym rozwiązaniem jest usunięcie bazy danych i użycie migracji w celu ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="95f3d-313">Aby usunąć bazę danych w programie SSOX:</span><span class="sxs-lookup"><span data-stu-id="95f3d-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="95f3d-314">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="95f3d-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="95f3d-315">Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="95f3d-316">Zaznacz pole wyboru **Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="95f3d-317">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="95f3d-317">Select **OK**.</span></span>
* <span data-ttu-id="95f3d-318">W obszarze [PMC](xref:tutorials/razor-pages/new-field#pmc)zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="95f3d-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="95f3d-319">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="95f3d-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="95f3d-320">Porzuć i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="95f3d-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="95f3d-321">W tym samouczku należy użyć funkcji *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="95f3d-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="95f3d-322">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="95f3d-323">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="95f3d-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="95f3d-324">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="95f3d-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="95f3d-325">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="95f3d-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="95f3d-326">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="95f3d-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="95f3d-327">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="95f3d-328">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="95f3d-329">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="95f3d-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="95f3d-330">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-330">Creating a new table.</span></span>
>* <span data-ttu-id="95f3d-331">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="95f3d-332">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-332">Dropping the old table.</span></span>
>* <span data-ttu-id="95f3d-333">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="95f3d-333">Renaming the new table.</span></span>
>
><span data-ttu-id="95f3d-334">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="95f3d-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="95f3d-335">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="95f3d-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="95f3d-336">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="95f3d-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="95f3d-337">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="95f3d-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="95f3d-338">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="95f3d-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="95f3d-339">Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95f3d-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="95f3d-340">Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie. DB*).</span><span class="sxs-lookup"><span data-stu-id="95f3d-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="95f3d-341">Następnie uruchom `ef database update` polecenie:</span><span class="sxs-lookup"><span data-stu-id="95f3d-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="95f3d-342">Uruchom aplikację i sprawdź, czy można tworzyć/edytować/wyświetlać filmy z `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="95f3d-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="95f3d-343">Jeśli baza danych nie jest zainicjowana, ustaw punkt przerwania w `SeedData.Initialize` metodzie.</span><span class="sxs-lookup"><span data-stu-id="95f3d-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95f3d-344">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="95f3d-344">Additional resources</span></span>

* [<span data-ttu-id="95f3d-345">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="95f3d-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="95f3d-346">[Poprzednie: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search) 
>  [Dalej: Dodawanie walidacji](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="95f3d-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end