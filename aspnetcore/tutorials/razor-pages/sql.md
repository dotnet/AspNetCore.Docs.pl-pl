---
title: Część 4, współpraca z bazą danych
author: rick-anderson
description: Część 4 z serii samouczków na Razor stronach.
ms.author: riande
ms.date: 01/05/2021
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3133ff7a192f5e3bdec4f6f1e6e6d55f966d7e64
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589767"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a><span data-ttu-id="22c57-103">Część 4 z serii samouczków na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="22c57-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="22c57-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="22c57-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="22c57-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="22c57-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="22c57-106">`RazorPagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="22c57-107">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22c57-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-109">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="22c57-110">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz.</span><span class="sxs-lookup"><span data-stu-id="22c57-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="22c57-111">W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="22c57-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="22c57-113">Wygenerowane parametry połączenia są podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="22c57-113">The generated connection string is similar to the following JSON:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-114">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="22c57-115">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="22c57-116">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="22c57-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="22c57-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="22c57-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="22c57-119">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="22c57-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="22c57-120">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="22c57-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="22c57-121">Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:\Users\<user>\` katalogu.</span><span class="sxs-lookup"><span data-stu-id="22c57-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="22c57-122">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="22c57-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menu Widok](sql/_static/5/ssox.png)

1. <span data-ttu-id="22c57-124">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków**:</span><span class="sxs-lookup"><span data-stu-id="22c57-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/5/design.png)

   ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

   <span data-ttu-id="22c57-127">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="22c57-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="22c57-128">Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="22c57-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="22c57-129">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane**:</span><span class="sxs-lookup"><span data-stu-id="22c57-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-131">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="22c57-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="22c57-132">SQLite</span></span>

<span data-ttu-id="22c57-133">Stan witryny sieci Web programu [SQLite](https://www.sqlite.org/) :</span><span class="sxs-lookup"><span data-stu-id="22c57-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="22c57-134">Oprogramowanie SQLite to samodzielna, osadzona, w pełni funkcjonalna, publiczna i ogólnie oparta na usłudze SQL Database aparat.</span><span class="sxs-lookup"><span data-stu-id="22c57-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="22c57-135">SQLite to najbardziej używany aparat bazy danych na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="22c57-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="22c57-136">Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych programu SQLite i ją przeglądać.</span><span class="sxs-lookup"><span data-stu-id="22c57-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="22c57-137">Poniższy obraz pochodzi z [przeglądarki DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="22c57-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="22c57-138">Jeśli masz ulubione narzędzie SQLite, pozostaw komentarz na temat tego, co Ci się podoba.</span><span class="sxs-lookup"><span data-stu-id="22c57-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Przeglądarka DB dla oprogramowania SQLite przedstawiająca bazę danych filmów](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="22c57-140">W tym samouczku zostanie użyta funkcja *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="22c57-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="22c57-141">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="22c57-142">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="22c57-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="22c57-143">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="22c57-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="22c57-144">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="22c57-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="22c57-145">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="22c57-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="22c57-146">Zamiast tego, po zmianie schematu baza danych zostanie porzucona i utworzona.</span><span class="sxs-lookup"><span data-stu-id="22c57-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="22c57-147">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="22c57-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="22c57-148">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="22c57-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="22c57-149">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-149">Creating a new table.</span></span>
>* <span data-ttu-id="22c57-150">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="22c57-151">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-151">Dropping the old table.</span></span>
>* <span data-ttu-id="22c57-152">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-152">Renaming the new table.</span></span>
>
><span data-ttu-id="22c57-153">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="22c57-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="22c57-154">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="22c57-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="22c57-155">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="22c57-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="22c57-156">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="22c57-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="22c57-157">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="22c57-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="22c57-158">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="22c57-158">Seed the database</span></span>

<span data-ttu-id="22c57-159">Utwórz nową klasę o nazwie `SeedData` w folderze *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="22c57-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="22c57-160">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="22c57-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="22c57-161">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="22c57-161">Add the seed initializer</span></span>

<span data-ttu-id="22c57-162">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="22c57-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="22c57-163">W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="22c57-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="22c57-164">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="22c57-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="22c57-165">Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="22c57-166">Usuń kontekst, gdy metoda inicjatora zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="22c57-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="22c57-167">[Instrukcja using](/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="22c57-167">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="22c57-168">Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="22c57-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="22c57-169">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="22c57-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="22c57-171">Usuń wszystkie rekordy z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-171">Delete all the records in the database.</span></span> <span data-ttu-id="22c57-172">Korzystanie z linków do usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="22c57-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="22c57-173">Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="22c57-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="22c57-174">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="22c57-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="22c57-175">Zatrzymaj i uruchom ponownie usługi IIS przy użyciu dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="22c57-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="22c57-176">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień, a następnie wybierz polecenie **Zakończ** lub **Zatrzymaj lokację**:</span><span class="sxs-lookup"><span data-stu-id="22c57-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menu kontekstowe](sql/_static/stopIIS.png)

   1. <span data-ttu-id="22c57-179">Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="22c57-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="22c57-180">Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="22c57-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-181">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="22c57-182">Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="22c57-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="22c57-183">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="22c57-184">Aplikacja pokazuje dane z rozrzutu:</span><span class="sxs-lookup"><span data-stu-id="22c57-184">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w przeglądarce przedstawiająca dane filmu](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="22c57-186">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="22c57-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="22c57-187">[Poprzednie: Rusztowania Razor ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="22c57-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="22c57-188">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="22c57-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="22c57-189">`RazorPagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="22c57-190">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22c57-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-192">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="22c57-193">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz.</span><span class="sxs-lookup"><span data-stu-id="22c57-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="22c57-194">W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="22c57-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="22c57-196">Wygenerowane parametry połączenia będą podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="22c57-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-197">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="22c57-198">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="22c57-199">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="22c57-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="22c57-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="22c57-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="22c57-202">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="22c57-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="22c57-203">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="22c57-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="22c57-204">Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:\Users\<user>\` katalogu.</span><span class="sxs-lookup"><span data-stu-id="22c57-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="22c57-205">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="22c57-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](sql/_static/ssox.png)

* <span data-ttu-id="22c57-207">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków**:</span><span class="sxs-lookup"><span data-stu-id="22c57-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu kontekstowe są otwierane w tabeli filmów](sql/_static/design.png)

  ![Tabele filmów otwierane w projektancie](sql/_static/dv.png)

<span data-ttu-id="22c57-210">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="22c57-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="22c57-211">Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="22c57-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="22c57-212">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane**:</span><span class="sxs-lookup"><span data-stu-id="22c57-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-214">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="22c57-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="22c57-215">SQLite</span></span>

<span data-ttu-id="22c57-216">Stan witryny sieci Web programu [SQLite](https://www.sqlite.org/) :</span><span class="sxs-lookup"><span data-stu-id="22c57-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="22c57-217">Oprogramowanie SQLite to samodzielna, osadzona, w pełni funkcjonalna, publiczna i ogólnie oparta na usłudze SQL Database aparat.</span><span class="sxs-lookup"><span data-stu-id="22c57-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="22c57-218">SQLite to najbardziej używany aparat bazy danych na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="22c57-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="22c57-219">Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych programu SQLite i ją przeglądać.</span><span class="sxs-lookup"><span data-stu-id="22c57-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="22c57-220">Poniższy obraz pochodzi z [przeglądarki DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="22c57-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="22c57-221">Jeśli masz ulubione narzędzie SQLite, pozostaw komentarz na temat tego, co Ci się podoba.</span><span class="sxs-lookup"><span data-stu-id="22c57-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Przeglądarka DB dla oprogramowania SQLite przedstawiająca bazę danych filmów](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="22c57-223">W tym samouczku zostanie użyta funkcja *migracji* Entity Framework Core, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="22c57-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="22c57-224">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="22c57-225">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="22c57-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="22c57-226">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="22c57-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="22c57-227">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="22c57-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="22c57-228">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="22c57-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="22c57-229">Zamiast tego, po zmianie schematu baza danych zostanie porzucona i utworzona.</span><span class="sxs-lookup"><span data-stu-id="22c57-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="22c57-230">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="22c57-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="22c57-231">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="22c57-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="22c57-232">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-232">Creating a new table.</span></span>
>* <span data-ttu-id="22c57-233">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="22c57-234">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-234">Dropping the old table.</span></span>
>* <span data-ttu-id="22c57-235">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="22c57-235">Renaming the new table.</span></span>
>
><span data-ttu-id="22c57-236">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="22c57-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="22c57-237">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="22c57-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="22c57-238">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="22c57-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="22c57-239">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="22c57-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="22c57-240">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="22c57-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="22c57-241">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="22c57-241">Seed the database</span></span>

<span data-ttu-id="22c57-242">Utwórz nową klasę o nazwie `SeedData` w folderze *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="22c57-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="22c57-243">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="22c57-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="22c57-244">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="22c57-244">Add the seed initializer</span></span>

<span data-ttu-id="22c57-245">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="22c57-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="22c57-246">W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="22c57-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="22c57-247">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="22c57-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="22c57-248">Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="22c57-249">Usuń kontekst, gdy metoda inicjatora zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="22c57-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="22c57-250">[Instrukcja using](/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="22c57-250">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="22c57-251">Następujący wyjątek występuje, gdy `Update-Database` nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="22c57-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="22c57-252">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="22c57-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="22c57-254">Usuń wszystkie rekordy z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-254">Delete all the records in the database.</span></span> <span data-ttu-id="22c57-255">Użyj linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span><span class="sxs-lookup"><span data-stu-id="22c57-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="22c57-256">Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="22c57-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="22c57-257">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="22c57-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="22c57-258">Zatrzymaj i uruchom ponownie usługi IIS przy użyciu dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="22c57-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="22c57-259">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**:</span><span class="sxs-lookup"><span data-stu-id="22c57-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * <span data-ttu-id="22c57-262">Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="22c57-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="22c57-263">Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="22c57-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-264">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="22c57-265">Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="22c57-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="22c57-266">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="22c57-267">Aplikacja pokazuje dane z rozrzutu:</span><span class="sxs-lookup"><span data-stu-id="22c57-267">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="22c57-269">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="22c57-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="22c57-270">[Poprzednie: Rusztowania Razor ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="22c57-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="22c57-271">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="22c57-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="22c57-272">`RazorPagesMovieContext`Obiekt obsługuje zadanie łączenia się z bazą danych i mapowania `Movie` obiektów do rekordów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="22c57-273">Kontekst bazy danych jest zarejestrowany z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) w `ConfigureServices` metodzie w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="22c57-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="22c57-275">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="22c57-276">Aby uzyskać więcej informacji na temat metod używanych w programie `ConfigureServices` , zobacz:</span><span class="sxs-lookup"><span data-stu-id="22c57-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="22c57-277">[Obsługa ogólne rozporządzenie o ochronie danych UE (Rodo) w ASP.NET Core](xref:security/gdpr) dla `CookiePolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="22c57-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="22c57-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="22c57-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="22c57-279">System [konfiguracji](xref:fundamentals/configuration/index) ASP.NET Core odczytuje `ConnectionString` klucz.</span><span class="sxs-lookup"><span data-stu-id="22c57-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="22c57-280">W przypadku lokalnego projektowania konfiguracja pobiera parametry połączenia z *appsettings.json* pliku.</span><span class="sxs-lookup"><span data-stu-id="22c57-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="22c57-282">Wygenerowane parametry połączenia będą podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="22c57-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="22c57-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="22c57-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="22c57-284">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="22c57-285">Gdy aplikacja jest wdrażana na serwerze testowym lub produkcyjnym, zmienna środowiskowa może służyć do ustawiania parametrów połączenia na serwerze testowym lub produkcyjnym bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="22c57-286">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="22c57-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="22c57-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="22c57-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="22c57-289">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express, która jest przeznaczona do tworzenia programów.</span><span class="sxs-lookup"><span data-stu-id="22c57-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="22c57-290">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="22c57-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="22c57-291">Domyślnie baza danych LocalDB tworzy `*.mdf` pliki w `C:/Users/<user/>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="22c57-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="22c57-292">Z menu **Widok** Otwórz **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="22c57-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu Widok](sql/_static/ssox.png)

* <span data-ttu-id="22c57-294">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Projektant widoków**:</span><span class="sxs-lookup"><span data-stu-id="22c57-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu kontekstowe jest otwarte w tabeli filmów](sql/_static/design.png)

  ![Tabela filmów otwarta w projektancie](sql/_static/dv.png)

<span data-ttu-id="22c57-297">Zanotuj ikonę klucza obok pozycji `ID` .</span><span class="sxs-lookup"><span data-stu-id="22c57-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="22c57-298">Domyślnie EF tworzy właściwość o nazwie `ID` dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="22c57-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="22c57-299">Kliknij prawym przyciskiem myszy `Movie` tabelę i wybierz polecenie **Wyświetl dane**:</span><span class="sxs-lookup"><span data-stu-id="22c57-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Otwórz tabelę filmów pokazującą dane tabeli](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="22c57-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="22c57-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="22c57-302">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="22c57-303">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="22c57-303">Seed the database</span></span>

<span data-ttu-id="22c57-304">Utwórz nową klasę o nazwie `SeedData` w folderze *models* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="22c57-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="22c57-305">Jeśli w bazie danych znajdują się jakiekolwiek filmy, inicjatora inicjatora zwraca i nie dodano żadnych filmów.</span><span class="sxs-lookup"><span data-stu-id="22c57-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="22c57-306">Dodawanie inicjatora inicjatora</span><span class="sxs-lookup"><span data-stu-id="22c57-306">Add the seed initializer</span></span>

<span data-ttu-id="22c57-307">Zastąp zawartość pliku *Program.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="22c57-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="22c57-308">W poprzednim kodzie `Main` Metoda została zmodyfikowana w celu wykonania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="22c57-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="22c57-309">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="22c57-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="22c57-310">Wywołaj `seedData.Initialize` metodę, przekazując do niej wystąpienie kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="22c57-311">Usuń kontekst, gdy metoda inicjatora zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="22c57-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="22c57-312">[Instrukcja using](/dotnet/csharp/language-reference/keywords/using-statement) gwarantuje, że kontekst zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="22c57-312">The [using statement](/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="22c57-313">Aplikacja produkcyjna nie będzie wywoływana `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="22c57-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="22c57-314">Jest on dodawany do poprzedniego kodu, aby zapobiec następującemu wyjątku, gdy `Update-Database` nie został uruchomiony:</span><span class="sxs-lookup"><span data-stu-id="22c57-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="22c57-315">SqlException: nie można otworzyć bazy danych " Razor PagesMovieContext-21" żądanej przez nazwę logowania.</span><span class="sxs-lookup"><span data-stu-id="22c57-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="22c57-316">Logowanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="22c57-316">The login failed.</span></span>
<span data-ttu-id="22c57-317">Logowanie użytkownika "Nazwa użytkownika" nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="22c57-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="22c57-318">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="22c57-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="22c57-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22c57-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="22c57-320">Usuń wszystkie rekordy z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-320">Delete all the records in the database.</span></span> <span data-ttu-id="22c57-321">Można to zrobić za pomocą linków usuwania w przeglądarce lub z [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="22c57-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="22c57-322">Wymuś inicjalizację aplikacji przez wywołanie metod w `Startup` klasie, więc metoda inicjatora jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="22c57-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="22c57-323">Aby wymusić inicjalizację, IIS Express należy zatrzymać i uruchomić ponownie.</span><span class="sxs-lookup"><span data-stu-id="22c57-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="22c57-324">Można to zrobić przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="22c57-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="22c57-325">Kliknij prawym przyciskiem myszy ikonę IIS Express pasku zadań w obszarze powiadomień i naciśnij pozycję **Zakończ** lub **Zatrzymaj witrynę**:</span><span class="sxs-lookup"><span data-stu-id="22c57-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Ikona paska zadań IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu kontekstowe](sql/_static/stopIIS.png)

    * <span data-ttu-id="22c57-328">Jeśli aplikacja działa w trybie innym niż debugowanie, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić w trybie debugowania.</span><span class="sxs-lookup"><span data-stu-id="22c57-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="22c57-329">Jeśli aplikacja w trybie debugowania, Zatrzymaj debuger i naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="22c57-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="22c57-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="22c57-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="22c57-331">Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="22c57-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="22c57-332">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="22c57-333">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="22c57-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="22c57-334">Usuń wszystkie rekordy z bazy danych, dzięki czemu zostanie uruchomiona Metoda inicjatora.</span><span class="sxs-lookup"><span data-stu-id="22c57-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="22c57-335">Zatrzymaj i uruchom aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="22c57-336">Aplikacja pokazuje dane z rozrzutu:</span><span class="sxs-lookup"><span data-stu-id="22c57-336">The app shows the seeded data:</span></span>

![Aplikacja filmowa otwarta w programie Chrome pokazująca dane filmu](sql/_static/m55https.png)

<span data-ttu-id="22c57-338">Następny samouczek czyści prezentację danych.</span><span class="sxs-lookup"><span data-stu-id="22c57-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22c57-339">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="22c57-339">Additional resources</span></span>

* [<span data-ttu-id="22c57-340">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="22c57-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="22c57-341">[Poprzednie: Rusztowania Razor ](xref:tutorials/razor-pages/page) 
>  [Następna strona: aktualizowanie stron](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="22c57-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end