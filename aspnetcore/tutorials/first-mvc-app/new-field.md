---
title: Dodawanie nowego pola do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak użyć pierwszej migracji kodu frameworka, aby dodać nowe pole do modelu i przeprowadzić migrację do bazy danych.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a5ea9b75cf8bb1f31cb07a2b32f361bdbfd4efa3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662905"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="c998d-103">Dodawanie nowego pola do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c998d-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="c998d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c998d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c998d-105">W tej sekcji [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations służy do:</span><span class="sxs-lookup"><span data-stu-id="c998d-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c998d-106">Dodaj nowe pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="c998d-106">Add a new field to the model.</span></span>
* <span data-ttu-id="c998d-107">Migrowanie nowego pola do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="c998d-108">Gdy EF Code First jest używany do automatycznego tworzenia bazy danych, Code First:</span><span class="sxs-lookup"><span data-stu-id="c998d-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c998d-109">Dodaje tabelę do bazy danych, aby śledzić schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="c998d-110">Weryfikuje bazy danych jest zsynchronizowany z klas modelu, który został wygenerowany z.</span><span class="sxs-lookup"><span data-stu-id="c998d-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="c998d-111">Jeśli nie są one zsynchronizowane, EF zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="c998d-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="c998d-112">Ułatwia to znajdowanie niespójnych problemów z bazą danych/kodem.</span><span class="sxs-lookup"><span data-stu-id="c998d-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c998d-113">Dodawanie właściwości Ocena do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="c998d-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="c998d-114">Dodaj `Rating` właściwość do *Models/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="c998d-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="c998d-115">Kompilacja aplikacji</span><span class="sxs-lookup"><span data-stu-id="c998d-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="c998d-116">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c998d-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="c998d-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="c998d-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="c998d-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c998d-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c998d-119">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c998d-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c998d-120">Polecenie ♠ + B</span><span class="sxs-lookup"><span data-stu-id="c998d-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="c998d-121">Ponieważ dodano nowe pole do `Movie` klasy, należy zaktualizować białą listę wiązania, aby ta nowa właściwość została uwzględniona.</span><span class="sxs-lookup"><span data-stu-id="c998d-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="c998d-122">W *MoviesController.cs*, zaktualizuj `[Bind]` atrybut `Create` `Edit` zarówno dla metod `Rating` akcji, jak i metod działania, aby uwzględnić właściwość:</span><span class="sxs-lookup"><span data-stu-id="c998d-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="c998d-123">Zaktualizuj szablony widoku, aby wyświetlić, utworzyć i edytować nową `Rating` właściwość w widoku przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c998d-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="c998d-124">Edytuj plik */Views/Movies/Index.cshtml* i `Rating` dodaj pole:</span><span class="sxs-lookup"><span data-stu-id="c998d-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="c998d-125">Zaktualizuj *pole /Views/Movies/Create.cshtml.* `Rating`</span><span class="sxs-lookup"><span data-stu-id="c998d-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="c998d-126">Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c998d-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="c998d-127">Możesz skopiować/wkleić poprzednią "grupę formularzy" i umożliwić intelliSense pomoc w aktualizacji pól.</span><span class="sxs-lookup"><span data-stu-id="c998d-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="c998d-128">IntelliSense współpracuje z [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="c998d-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Deweloper wpisał literę R dla wartości atrybutu asp-for w drugim elemencie etykiety widoku.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c998d-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c998d-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="c998d-133">Zaktualizuj pozostałe szablony.</span><span class="sxs-lookup"><span data-stu-id="c998d-133">Update the remaining templates.</span></span>

<span data-ttu-id="c998d-134">Zaktualizuj `SeedData` klasę tak, aby zapewniała wartość dla nowej kolumny.</span><span class="sxs-lookup"><span data-stu-id="c998d-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c998d-135">Przykładowa zmiana jest pokazana poniżej, ale warto `new Movie`wprowadzić tę zmianę dla każdego .</span><span class="sxs-lookup"><span data-stu-id="c998d-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="c998d-136">Aplikacja nie będzie działać, dopóki baza danych nie zostanie zaktualizowana w celu uwzględnienia nowego pola.</span><span class="sxs-lookup"><span data-stu-id="c998d-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="c998d-137">Jeśli jest uruchomiony teraz, `SqlException` są generowane następujące:</span><span class="sxs-lookup"><span data-stu-id="c998d-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c998d-138">Ten błąd występuje, ponieważ zaktualizowana klasa modelu Movie różni się od schematu tabeli Movie istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="c998d-139">(W tabeli `Rating` bazy danych nie ma żadnej kolumny).</span><span class="sxs-lookup"><span data-stu-id="c998d-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="c998d-140">Istnieje kilka podejść do rozwiązania błędu:</span><span class="sxs-lookup"><span data-stu-id="c998d-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c998d-141">Czy entity framework automatycznie upuścić i ponownie utworzyć bazę danych na podstawie nowego schematu klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="c998d-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="c998d-142">Takie podejście jest bardzo wygodne na początku cyklu rozwoju, gdy robisz aktywnego rozwoju w testowej bazie danych; pozwala szybko rozwijać schemat modelu i bazy danych razem.</span><span class="sxs-lookup"><span data-stu-id="c998d-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c998d-143">Minusem jest jednak to, że tracisz istniejące dane w bazie danych — więc nie chcesz używać tego podejścia w produkcyjnej bazie danych!</span><span class="sxs-lookup"><span data-stu-id="c998d-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="c998d-144">Za pomocą inicjatora automatycznie seed bazy danych z danymi testowymi jest często produktywny sposób tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c998d-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="c998d-145">Jest to dobre podejście do wczesnego rozwoju i przy użyciu SQLite.</span><span class="sxs-lookup"><span data-stu-id="c998d-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="c998d-146">Jawnie zmodyfikuj schemat istniejącej bazy danych, tak aby odpowiadała klasom modelu.</span><span class="sxs-lookup"><span data-stu-id="c998d-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c998d-147">Zaletą tego podejścia jest przechowywanie danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="c998d-148">Tę zmianę można wprowadzić ręcznie lub tworząc skrypt zmiany bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c998d-149">Użyj migracji code first, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c998d-150">W tym samouczku używane są migracje code first.</span><span class="sxs-lookup"><span data-stu-id="c998d-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c998d-151">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c998d-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c998d-152">Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="c998d-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menu PMC](adding-model/_static/pmc.png)

<span data-ttu-id="c998d-154">W pmc wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="c998d-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="c998d-155">Polecenie `Add-Migration` informuje strukturę migracji, `Movie` aby zbadać `Movie` bieżący model z bieżącym schematem bazy danych i utworzyć kod niezbędny do migracji bazy danych do nowego modelu.</span><span class="sxs-lookup"><span data-stu-id="c998d-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="c998d-156">Nazwa "Ocena" jest dowolna i jest używana do nadawanie nazwy plikowi migracji.</span><span class="sxs-lookup"><span data-stu-id="c998d-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c998d-157">Warto użyć znaczącej nazwy pliku migracji.</span><span class="sxs-lookup"><span data-stu-id="c998d-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c998d-158">Jeśli wszystkie rekordy w bazy danych zostaną usunięte, metoda inicjowania `Rating` spowoduje wysiew bazy danych i uwzględni pole.</span><span class="sxs-lookup"><span data-stu-id="c998d-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c998d-159">Kod programu Visual Studio / Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="c998d-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="c998d-160">Usuń bazę danych i użyj migracji, aby ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="c998d-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c998d-161">Aby usunąć bazę danych, usuń plik bazy danych (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="c998d-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="c998d-162">Następnie uruchom `ef database update` polecenie:</span><span class="sxs-lookup"><span data-stu-id="c998d-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="c998d-163">Uruchom aplikację i sprawdź, czy możesz tworzyć, `Rating` edytować i wyświetlać filmy za pomocą pola.</span><span class="sxs-lookup"><span data-stu-id="c998d-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span> <span data-ttu-id="c998d-164">Zaktualizuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="c998d-164">Update the app:</span></span>

* <span data-ttu-id="c998d-165">Dodaj `Rating` to pole `Edit` `Details`do `Delete` przycisku , i wyświetl szablony.</span><span class="sxs-lookup"><span data-stu-id="c998d-165">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>
* <span data-ttu-id="c998d-166">Zaktualizuj powiązanie w `MoviesController`metodzie akcji edycji programu .</span><span class="sxs-lookup"><span data-stu-id="c998d-166">Update the binding in the edit action method of the `MoviesController`.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c998d-167">[Poprzedni](search.md)
> [następny](validation.md)</span><span class="sxs-lookup"><span data-stu-id="c998d-167">[Previous](search.md)
[Next](validation.md)</span></span>
