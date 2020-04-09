---
title: Dodawanie nowego pola do strony razor w ASP.NET Core
author: rick-anderson
description: Pokazuje, jak dodać nowe pole do strony Razor ze wzorem Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d34b938dbd1b512ddb167cac0c035837889cd38f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657816"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="443f0-103">Dodawanie nowego pola do strony razor w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="443f0-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="443f0-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="443f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="443f0-105">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations służy do:</span><span class="sxs-lookup"><span data-stu-id="443f0-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="443f0-106">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-106">Add a new field to the model.</span></span>
* <span data-ttu-id="443f0-107">Migrowanie zmiany nowego schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="443f0-108">Podczas korzystania z EF Code First do automatycznego tworzenia bazy danych, Code First:</span><span class="sxs-lookup"><span data-stu-id="443f0-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="443f0-109">Dodaje `__EFMigrationsHistory` tabelę do bazy danych, aby śledzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z wygenerowali.</span><span class="sxs-lookup"><span data-stu-id="443f0-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="443f0-110">Jeśli klasy modelu nie są zsynchronizowane z bazy danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="443f0-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="443f0-111">Automatyczna weryfikacja schematu/modelu w synchronizacji ułatwia znajdowanie niespójnych problemów z bazą danych/kodem.</span><span class="sxs-lookup"><span data-stu-id="443f0-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="443f0-112">Dodawanie właściwości Ocena do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="443f0-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="443f0-113">Otwórz plik *Models/Movie.cs* i `Rating` dodaj właściwość:</span><span class="sxs-lookup"><span data-stu-id="443f0-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="443f0-114">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="443f0-114">Build the app.</span></span>

<span data-ttu-id="443f0-115">Edytuj *strony/filmy/index.cshtml*i `Rating` dodaj pole:</span><span class="sxs-lookup"><span data-stu-id="443f0-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="443f0-116">Zaktualizuj następujące strony:</span><span class="sxs-lookup"><span data-stu-id="443f0-116">Update the following pages:</span></span>

* <span data-ttu-id="443f0-117">Dodaj `Rating` to pole do stron Usuń i Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="443f0-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="443f0-118">Zaktualizuj plik `Rating` [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) za pomocą pola.</span><span class="sxs-lookup"><span data-stu-id="443f0-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="443f0-119">Dodaj `Rating` to pole do strony edytuj.</span><span class="sxs-lookup"><span data-stu-id="443f0-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="443f0-120">Aplikacja nie będzie działać, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="443f0-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="443f0-121">Uruchamianie aplikacji bez aktualizowania bazy `SqlException`danych zgłasza:</span><span class="sxs-lookup"><span data-stu-id="443f0-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="443f0-122">Wyjątek `SqlException` jest spowodowany przez zaktualizowane Movie model klasy jest inny niż schemat Movie tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="443f0-123">(W tabeli `Rating` bazy danych nie ma żadnej kolumny).</span><span class="sxs-lookup"><span data-stu-id="443f0-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="443f0-124">Istnieje kilka podejść do rozwiązania błędu:</span><span class="sxs-lookup"><span data-stu-id="443f0-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="443f0-125">Czy entity framework automatycznie upuścić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="443f0-126">Takie podejście jest wygodne na początku cyklu rozwoju; pozwala szybko rozwijać schemat modelu i bazy danych razem.</span><span class="sxs-lookup"><span data-stu-id="443f0-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="443f0-127">Wadą jest utrata istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="443f0-128">Nie używaj tego podejścia w produkcyjnej bazie danych!</span><span class="sxs-lookup"><span data-stu-id="443f0-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="443f0-129">Upuszczanie bazy danych na zmiany schematu i przy użyciu inicjatora automatycznie seed bazy danych z danymi testowymi jest często produktywny sposób tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="443f0-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="443f0-130">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby odpowiadała klasom modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="443f0-131">Zaletą tego podejścia jest przechowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="443f0-132">Tę zmianę można wprowadzić ręcznie lub tworząc skrypt zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="443f0-133">Użyj migracji code first, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="443f0-134">W tym samouczku użyj migracji code first.</span><span class="sxs-lookup"><span data-stu-id="443f0-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="443f0-135">Zaktualizuj `SeedData` klasę tak, aby zapewniała wartość dla nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="443f0-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="443f0-136">Przykładowa zmiana jest wyświetlana poniżej, ale warto `new Movie` wprowadzić tę zmianę dla każdego bloku.</span><span class="sxs-lookup"><span data-stu-id="443f0-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="443f0-137">Zobacz [wypełniony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="443f0-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="443f0-138">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="443f0-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="443f0-139">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="443f0-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="443f0-140">Dodawanie migracji dla pola klasyfikacji</span><span class="sxs-lookup"><span data-stu-id="443f0-140">Add a migration for the rating field</span></span>

<span data-ttu-id="443f0-141">Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="443f0-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="443f0-142">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="443f0-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="443f0-143">Polecenie `Add-Migration` informuje framework do:</span><span class="sxs-lookup"><span data-stu-id="443f0-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="443f0-144">Porównaj `Movie` model `Movie` ze schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="443f0-145">Utwórz kod, aby przeprowadzić migrację schematu bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="443f0-146">Nazwa "Ocena" jest dowolna i jest używana do nadawanie nazwy plikowi migracji.</span><span class="sxs-lookup"><span data-stu-id="443f0-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="443f0-147">Warto użyć znaczącej nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="443f0-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="443f0-148">Polecenie `Update-Database` informuje strukturę, aby zastosować zmiany schematu do bazy danych i zachować istniejące dane.</span><span class="sxs-lookup"><span data-stu-id="443f0-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="443f0-149">Jeśli usuniesz wszystkie rekordy w bazy danych, inicjator wysiewu bazy danych i uwzględni `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="443f0-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="443f0-150">Można to zrobić za pomocą łączy usuwania w przeglądarce lub z [Programu Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="443f0-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="443f0-151">Inną opcją jest usunięcie bazy danych i użycie migracji do ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="443f0-152">Aby usunąć bazę danych w SSOX:</span><span class="sxs-lookup"><span data-stu-id="443f0-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="443f0-153">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="443f0-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="443f0-154">Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie *Usuń*.</span><span class="sxs-lookup"><span data-stu-id="443f0-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="443f0-155">Zaznacz **pole wyboru Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="443f0-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="443f0-156">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="443f0-156">Select **OK**.</span></span>
* <span data-ttu-id="443f0-157">W [PMC](xref:tutorials/razor-pages/new-field#pmc), zaktualizować bazę danych:</span><span class="sxs-lookup"><span data-stu-id="443f0-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="443f0-158">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="443f0-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="443f0-159">Upuść i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="443f0-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="443f0-160">Usuń folder migracji.</span><span class="sxs-lookup"><span data-stu-id="443f0-160">Delete the migration folder.</span></span>  <span data-ttu-id="443f0-161">Użyj następujących poleceń, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="443f0-162">Uruchom aplikację i sprawdź, czy możesz tworzyć/edytować/wyświetlać filmy z polem. `Rating`</span><span class="sxs-lookup"><span data-stu-id="443f0-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="443f0-163">Jeśli baza danych nie jest rozstawiona, `SeedData.Initialize` ustaw punkt przerwania w metodzie.</span><span class="sxs-lookup"><span data-stu-id="443f0-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="443f0-164">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="443f0-164">Additional resources</span></span>

* [<span data-ttu-id="443f0-165">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="443f0-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="443f0-166">[Poprzedni: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)
> [dalej: Dodawanie sprawdzania poprawności](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="443f0-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="443f0-167">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations służy do:</span><span class="sxs-lookup"><span data-stu-id="443f0-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="443f0-168">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-168">Add a new field to the model.</span></span>
* <span data-ttu-id="443f0-169">Migrowanie zmiany nowego schematu pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="443f0-170">Podczas korzystania z EF Code First do automatycznego tworzenia bazy danych, Code First:</span><span class="sxs-lookup"><span data-stu-id="443f0-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="443f0-171">Dodaje tabelę do bazy danych, aby śledzić, czy schemat bazy danych jest zsynchronizowany z klasami modelu, z wygenerowali.</span><span class="sxs-lookup"><span data-stu-id="443f0-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="443f0-172">Jeśli klasy modelu nie są zsynchronizowane z bazy danych, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="443f0-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="443f0-173">Automatyczna weryfikacja schematu/modelu w synchronizacji ułatwia znajdowanie niespójnych problemów z bazą danych/kodem.</span><span class="sxs-lookup"><span data-stu-id="443f0-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="443f0-174">Dodawanie właściwości Ocena do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="443f0-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="443f0-175">Otwórz plik *Models/Movie.cs* i `Rating` dodaj właściwość:</span><span class="sxs-lookup"><span data-stu-id="443f0-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="443f0-176">Kompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="443f0-176">Build the app.</span></span>

<span data-ttu-id="443f0-177">Edytuj *strony/filmy/index.cshtml*i `Rating` dodaj pole:</span><span class="sxs-lookup"><span data-stu-id="443f0-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="443f0-178">Zaktualizuj następujące strony:</span><span class="sxs-lookup"><span data-stu-id="443f0-178">Update the following pages:</span></span>

* <span data-ttu-id="443f0-179">Dodaj `Rating` to pole do stron Usuń i Szczegóły.</span><span class="sxs-lookup"><span data-stu-id="443f0-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="443f0-180">Zaktualizuj plik `Rating` [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) za pomocą pola.</span><span class="sxs-lookup"><span data-stu-id="443f0-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="443f0-181">Dodaj `Rating` to pole do strony edytuj.</span><span class="sxs-lookup"><span data-stu-id="443f0-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="443f0-182">Aplikacja nie będzie działać, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="443f0-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="443f0-183">Jeśli działa teraz, aplikacja `SqlException`zgłasza :</span><span class="sxs-lookup"><span data-stu-id="443f0-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="443f0-184">Ten błąd jest spowodowany przez zaktualizowaną klasę modelu Movie jest inny niż schemat Movie tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="443f0-185">(W tabeli `Rating` bazy danych nie ma żadnej kolumny).</span><span class="sxs-lookup"><span data-stu-id="443f0-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="443f0-186">Istnieje kilka podejść do rozwiązania błędu:</span><span class="sxs-lookup"><span data-stu-id="443f0-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="443f0-187">Czy entity framework automatycznie upuścić i ponownie utworzyć bazę danych przy użyciu nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="443f0-188">Takie podejście jest wygodne na początku cyklu rozwoju; pozwala szybko rozwijać schemat modelu i bazy danych razem.</span><span class="sxs-lookup"><span data-stu-id="443f0-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="443f0-189">Wadą jest utrata istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="443f0-190">Nie używaj tego podejścia w produkcyjnej bazie danych!</span><span class="sxs-lookup"><span data-stu-id="443f0-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="443f0-191">Upuszczanie bazy danych na zmiany schematu i przy użyciu inicjatora automatycznie seed bazy danych z danymi testowymi jest często produktywny sposób tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="443f0-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="443f0-192">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby odpowiadała klasom modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="443f0-193">Zaletą tego podejścia jest przechowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="443f0-194">Tę zmianę można wprowadzić ręcznie lub tworząc skrypt zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="443f0-195">Użyj migracji code first, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="443f0-196">W tym samouczku użyj migracji code first.</span><span class="sxs-lookup"><span data-stu-id="443f0-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="443f0-197">Zaktualizuj `SeedData` klasę tak, aby zapewniała wartość dla nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="443f0-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="443f0-198">Przykładowa zmiana jest wyświetlana poniżej, ale warto `new Movie` wprowadzić tę zmianę dla każdego bloku.</span><span class="sxs-lookup"><span data-stu-id="443f0-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="443f0-199">Zobacz [wypełniony plik SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="443f0-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="443f0-200">Skompiluj rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="443f0-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="443f0-201">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="443f0-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="443f0-202">Dodawanie migracji dla pola klasyfikacji</span><span class="sxs-lookup"><span data-stu-id="443f0-202">Add a migration for the rating field</span></span>

<span data-ttu-id="443f0-203">Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="443f0-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="443f0-204">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="443f0-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="443f0-205">Polecenie `Add-Migration` informuje framework do:</span><span class="sxs-lookup"><span data-stu-id="443f0-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="443f0-206">Porównaj `Movie` model `Movie` ze schematem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="443f0-207">Utwórz kod, aby przeprowadzić migrację schematu bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="443f0-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="443f0-208">Nazwa "Ocena" jest dowolna i jest używana do nadawanie nazwy plikowi migracji.</span><span class="sxs-lookup"><span data-stu-id="443f0-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="443f0-209">Warto użyć znaczącej nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="443f0-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="443f0-210">Polecenie `Update-Database` informuje strukturę, aby zastosować zmiany schematu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="443f0-211">Jeśli usuniesz wszystkie rekordy w bazy danych, inicjator wysiewu bazy danych i uwzględni `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="443f0-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="443f0-212">Można to zrobić za pomocą łączy usuwania w przeglądarce lub z [Programu Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="443f0-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="443f0-213">Inną opcją jest usunięcie bazy danych i użycie migracji do ponownego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="443f0-214">Aby usunąć bazę danych w SSOX:</span><span class="sxs-lookup"><span data-stu-id="443f0-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="443f0-215">Wybierz bazę danych w SSOX.</span><span class="sxs-lookup"><span data-stu-id="443f0-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="443f0-216">Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie *Usuń*.</span><span class="sxs-lookup"><span data-stu-id="443f0-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="443f0-217">Zaznacz **pole wyboru Zamknij istniejące połączenia**.</span><span class="sxs-lookup"><span data-stu-id="443f0-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="443f0-218">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="443f0-218">Select **OK**.</span></span>
* <span data-ttu-id="443f0-219">W [PMC](xref:tutorials/razor-pages/new-field#pmc), zaktualizować bazę danych:</span><span class="sxs-lookup"><span data-stu-id="443f0-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="443f0-220">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="443f0-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="443f0-221">Upuść i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="443f0-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="443f0-222">Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="443f0-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="443f0-223">Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="443f0-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="443f0-224">Następnie uruchom `ef database update` polecenie:</span><span class="sxs-lookup"><span data-stu-id="443f0-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="443f0-225">Uruchom aplikację i sprawdź, czy możesz tworzyć/edytować/wyświetlać filmy z polem. `Rating`</span><span class="sxs-lookup"><span data-stu-id="443f0-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="443f0-226">Jeśli baza danych nie jest rozstawiona, `SeedData.Initialize` ustaw punkt przerwania w metodzie.</span><span class="sxs-lookup"><span data-stu-id="443f0-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="443f0-227">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="443f0-227">Additional resources</span></span>

* [<span data-ttu-id="443f0-228">Wersja tego samouczka w YouTube</span><span class="sxs-lookup"><span data-stu-id="443f0-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="443f0-229">[Poprzedni: Dodawanie wyszukiwania](xref:tutorials/razor-pages/search)
> [dalej: Dodawanie sprawdzania poprawności](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="443f0-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
